```python
@dsl.pipeline(
    name='XGBoost Trainer',
    description='A trainer that does end-to-end distributed training for XGBoost models.'
)
def xgb_train_pipeline(
    output='gs://your-gcs-bucket',
    project='your-gcp-project',
    cluster_name='xgb-%s' % dsl.RUN_ID_PLACEHOLDER,
    region='us-central1',
    train_data='gs://ml-pipeline-playground/sfpd/train.csv',
    eval_data='gs://ml-pipeline-playground/sfpd/eval.csv',
    schema='gs://ml-pipeline-playground/sfpd/schema.json',
    target='resolution',
    rounds=200,
    workers=2,
    true_label='ACTION',
):
    output_template = str(output) + '/' + dsl.RUN_ID_PLACEHOLDER + '/data'

    # Current GCP pyspark/spark op do not provide outputs as return values, instead,
    # we need to use strings to pass the uri around.
    analyze_output = output_template
    transform_output_train = os.path.join(output_template, 'train', 'part-*')
    transform_output_eval = os.path.join(output_template, 'eval', 'part-*')
    train_output = os.path.join(output_template, 'train_output')
    predict_output = os.path.join(output_template, 'predict_output')

    with dsl.ExitHandler(exit_op=dataproc_delete_cluster_op(
        project_id=project,
        region=region,
        name=cluster_name
    )):
        _create_cluster_op = dataproc_create_cluster_op(
            project_id=project,
            region=region,
            name=cluster_name,
            initialization_actions=[
              os.path.join(_PYSRC_PREFIX,
                           'initialization_actions.sh'),
            ],
            image_version='1.2'
        )

        _analyze_op = dataproc_analyze_op(
            project=project,
            region=region,
            cluster_name=cluster_name,
            schema=schema,
            train_data=train_data,
            output=output_template
        ).after(_create_cluster_op).set_display_name('Analyzer')

        _transform_op = dataproc_transform_op(
            project=project,
            region=region,
            cluster_name=cluster_name,
            train_data=train_data,
            eval_data=eval_data,
            target=target,
            analysis=analyze_output,
            output=output_template
        ).after(_analyze_op).set_display_name('Transformer')

        _train_op = dataproc_train_op(
            project=project,
            region=region,
            cluster_name=cluster_name,
            train_data=transform_output_train,
            eval_data=transform_output_eval,
            target=target,
            analysis=analyze_output,
            workers=workers,
            rounds=rounds,
            output=train_output
        ).after(_transform_op).set_display_name('Trainer')

        _predict_op = dataproc_predict_op(
            project=project,
            region=region,
            cluster_name=cluster_name,
            data=transform_output_eval,
            model=train_output,
            target=target,
            analysis=analyze_output,
            output=predict_output
        ).after(_train_op).set_display_name('Predictor')

        _cm_op = confusion_matrix_op(
            predictions=os.path.join(predict_output, 'part-*.csv'),
            output_dir=output_template
        ).after(_predict_op)

        _roc_op = roc_op(
            predictions_dir=os.path.join(predict_output, 'part-*.csv'),
            true_class=true_label,
            true_score_column=true_label,
            output_dir=output_template
        ).after(_predict_op)

    dsl.get_pipeline_conf().add_op_transformer(
        gcp.use_gcp_secret('user-gcp-sa'))
```