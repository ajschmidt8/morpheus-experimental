
# Phishing URL Detection Morpheus Pipeline for AppShield Data

Experimental Morpheus pipeline using Docker containers for Triton Inference server and Morpheus SDK/Client.

## Setup Triton Inference Server

##### Pull Triton Inference Server Docker Image
Pull Docker image from [NGC] suitable for your environment.

```bash
docker pull nvcr.io/nvidia/tritonserver:22.08-py3
```

##### Start Triton Inference Server container

Change to a pipeline directory:

```bash
cd ${MORPHEUS_EXPERIMENTAL_ROOT}/phishing-url-detection/morpheus-pipeline
```

# Run Triton in explicit mode
```bash
docker run --rm -ti --gpus=all -p8000:8000 -p8001:8001 -p8002:8002 -v $PWD/models:/models/triton-model-repo nvcr.io/nvidia/tritonserver:22.08-py3 \
   tritonserver --model-repository=/models/triton-model-repo \
                --exit-on-error=false \
                --model-control-mode=explicit \
                --load-model phishurl-appshield-combined-lstm-dnn-onnx
```

##### Verify Model Deployment
Once Triton server finishes starting up, it will display the status of all loaded models. Successful deployment of the model will show the following:

```
+-------------------------------------------+---------+--------+
| Model                                     | Version | Status |
+-------------------------------------------+---------+--------+
| phishurl-appshield-combined-lstm-dnn-onnx | 1       | READY  |
+-------------------------------------------+---------+--------+
```

##### Build and Run Morpheus Container

Now that the model has been deployed successfully. For the experimental pipeline to execute, let's build a Morpheus container if one does not already exist.

**Note**: Before running the Morpheus container, we would need to supply an additional docker parameter to bind the Morpheus experimental pipeline repo to the container as a volume as shown in the exammple.

Build the release container as instructed in the [Build Morpheus Container] section of [Getting Started with Morpheus] document.

Example:
```bash
DOCKER_EXTRA_ARGS="-v ${MORPHEUS_EXPERIMENTAL_ROOT}:/workspace/morpheus_experimental" ./docker/run_container_release.sh
```

## Requirements
**Note**: Make sure below dependencies are installed in your environment before running the DGA detection pipeline. Run the installation command specified below if not.

```bash
pip install tldextract==3.3.1
```


## Run Pipeline
Launch the example using the following

```bash
cd ${MORPHEUS_ROOT}/morpheus_experimental/phishing-url-detection

python morpheus-pipeline/run.py --num_threads=1 \
              --server_url=localhost:8001 \
              --model_name=phishurl-appshield-combined-lstm-dnn-onnx \
              --input_glob=./morpheus-pipeline/data/URLS_Snapshots/snapshot-*/*.json \
              --tokenizer_path=./morpheus-pipeline/tokenizer_urls.csv \
              --max_min_norm_path=./morpheus-pipeline/max_min_urls.csv \
              --alexa_data_path=./datasets/alexa-top-500k.csv \
              --output_file=./phishing_url_detection_output.jsonlines
```

The configuration options for this example can be queried with:

```bash
python morpheus-pipeline/run.py --help
```

```
Usage: run.py [OPTIONS]

Options:
  --use_cpp BOOLEAN               Use C++ implementation. Default value is False
  --num_threads INTEGER RANGE     Number of internal pipeline threads to use
                                  [x>=1]
  --pipeline_batch_size INTEGER RANGE
                                  Internal batch size for the pipeline. Can be
                                  much larger than the model batch size. Also
                                  used for Kafka consumers  [x>=1]
  --model_max_batch_size INTEGER RANGE
                                  Max batch size to use for the model  [x>=1]
  --model_fea_length INTEGER RANGE
                                  Features length to use for the model  [x>=1]
  --model_name TEXT               The name of the model that is deployed on
                                  Tritonserver
  --server_url TEXT               Tritonserver url  [required]
  --input_glob TEXT               Input glob  [required]
  --tokenizer_path TEXT           Tokenizer path  [required]
  --max_min_norm_path TEXT        Max min normalization path  [required]
  --alexa_data_path TEXT          Alexa data path  [required]
  --output_file TEXT              The path to the file where the inference
                                  output will be saved.
  --watch_directory BOOLEAN       The watch directory option instructs this
                                  stage to not close down once all files have
                                  been read. Instead it will read all files
                                  that match the 'input_glob' pattern, and
                                  then continue to watch the directory for
                                  additional files. Any new files that are
                                  added that match the glob will then be
                                  processed.
  --help                          Show this message and exit.
  ```

  [NGC]: https://ngc.nvidia.com/catalog/containers/nvidia:tritonserver
  [Getting Started with Morpheus]:https://github.com/nv-morpheus/Morpheus#getting-started-with-morpheus
  [Build Morpheus Container]: https://github.com/nv-morpheus/Morpheus#build-morpheus-container