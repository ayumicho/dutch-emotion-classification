# Test Data
This directory contains the test dataset used for evaluating the models. 

### test.csv
The test set, `test.csv`, includes episode transcriptions from the Dutch television show Meester Frank Visser doet uitspraak. This was transcribed using the current Content Intelligence Agency pipeline, annotated, and manually reviewed by students. It consists of 828 lines. It is important to note that the original test set, `group4.csv`, did not include sentences labeled as "disgust. " Consequently, I manually added 15 sentences using ChatGPT to ensure the test set encompassed all emotions.

## Usage
To use the test dataset, load the `test.csv` file into your data processing pipeline.
