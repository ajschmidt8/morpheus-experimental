# Morpheus-Experimental

Morpheus-Experimental is a staging/collaboration/experimental area for development. This directory contains prototypes and pipelines which are still being developed and are in the alpha testing stage. Please be advised that the pipelines in Morpheus-Experimental are subject to change and we do not guarantee any compatibility between releases.

Prototype contributions should include at minimum a tutorial-style notebook, model file, sample data, inference script, and documentation. 

## Repo Structure
Every prototype has its own directory that contains everything belonging to the specific prototype. Directories can include the following subfolders:

### models
Model files for public release (ONNX preferred to pytorch/tensorflow)

### datasets

Sample of training data and validation dataset(input and output files) to be used to test pipeline

### training-tuning

A script showing how to train or fine-tune the model. It takes sample training data file as an input and creates a model file. It is reliable and repeatable (set seed values). If variables are used in script (epochs, learning_rate) set defaults to those used to achieve metrics reported in model card/documentation. It includes a `requirements.txt` file with dependencies and versions used for training. 

### inference

A non-morpheus pipeline script that contains data loading, preprocessing, model loading, inference, postprocessing, and serialized output file. It uses desired morpheus pipeline variables as input variables to the script (ie. threshold=0.6, hash_vocab_file=bert-base-cased). It produces a reliable and repeatable output file from the validation input dataset. It includes `requirements.txt` file with dependencies and versions used for non-morpheus inference.

### pipeline (optional)
All the necessary files for a full Morpheus pipeline of the prototype.

### model documentation

A readme.md that contains the following information for each model:

 - **Model/prototype name** - Name of the model
 - **Use case** - Specific use case the model targets
 - **Owner** - Name of the individual who owns the model
 - **Version** - Version of the model (major.minor.patch)
 - **Model overview** - General description
 - **Model architecture** - General model architecture
 - **Training** - Training dataset and paradigm
 - **How to use this model** - Circumstances where this model is useful
 - **Input data** - Typical data that is used as input to the model
 - **Output** - Type and format of model output
 - **Out-of-scope use cases** - Use cases not envisioned during development
 - **Ethical considerations** - Ethical analysis of risks and harms
 - **References** - Resources used in model development
 - **Training epochs** - Number of epochs used during training
 - **Batch size** - Batch size used during training
 - **GPU model** - Family of GPU used during training
 - **Model accuracy** - Accuracy of the model when tested
 - **Model F1** - F1 score of the model when tested
 - **Memory footprint** - Memory required by the model



# Model Card Info

## DGA Detection via AppShield
### Model Overview
This model is a convolution neural network model trained to classify URL domains generated by Domain-Generation-Algorithms. Domain generation algorithms (DGA) are algorithms seen in various families of malware that are used to periodically generate a large number of domain names that can be used as rendezvous points with their command and control servers.
### Model Architecture
There are two models for this use case. Both are CNNs. One is a binary classifier (DGA or benign), and the other classifies the specific DGA family the URL belongs to.
### Training
Training data consists of 320K labelled as DGA domains of 17 known DGA families and 710K labelled as not DGA domains.  
### How To Use This Model
Combined with host data from DOCA AppShield, this model can be used to detect DGA malware. A training notebook is also included so that users can update the model as more labeled data is collected. 
#### Input
his model is based on DOCA AppShield and the input of the model is the URL plugin which contains list of URLs connected to host processes.
#### Output
Binary classifier outputs process with URLs classified as DGA or benign. DGA family detection classifier outputs DGA family name.
### References
- https://data.netlab.360.com/dga/
- https://underdefense.com/guides/detecting-dga-domains-machine-learning-approach/
- https://developer.nvidia.com/networking/doca 


## Phishing URL Detection via AppShield
### Model Overview
This model is a binary classifier to label phishing URLs and non-phishing URLs obtained from host process data.
### Model Architecture
This model is a LSTM neural network with a fully connected layer to differentiate between legitimate URLs and phishing URLs. Features are derived both from the structure of the URL and the characters in the URL. 
### Training
Training data consists of 97K URLs labelled as phishing URLs and 100K URLs labelled as legitimate URLs.  
### How To Use This Model
Combined with host data from DOCA AppShield, this model can be used to detect DGA malware. A training notebook is also included so that users can update the model as more labeled data is collected. 
#### Input
Snapshots of URL plugins collected from DOCA AppShield
#### Output
Processes with URLs classified as phishing or non-phishing
### References
- https://github.com/Antimalweb/URLNet 
- https://developer.nvidia.com/networking/doca 




