# Training Data
This directory contains the training dataset used for developing the models.

### train.csv
The `train.csv` dataset contains episode transcriptions from various Dutch Television shows, such as Temptation Island, Meester Frank Visser doet uitspraak and Expeditie Robinson. The audio is transcribed with the current Content Intelligence Agency pipeline, which uses the OpenAI API and is annotated by students. The result is a dataset of 10402 lines for which the emotions are manually reviewed.

## Usage
To use the training dataset, load the `train.csv` file into your data processing pipeline.