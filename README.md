### Install dependencies

pip install -r  requirements.txt


cd model

# split training set and evaluation set while ensuring no training example in the evaluation set
# usage: build_datasets.py <input path> <distribution (default: 6)>
./build_datasets.py ../datasets/web

# transform images (normalized pixel values and resized pictures) in training dataset to numpy arrays (smaller files if you need to upload the set to train your model in the cloud)
# usage: convert_imgs_to_arrays.py <input path> <output path>
./convert_imgs_to_arrays.py ../datasets/web/training_set ../datasets/web/training_features
```

Train the model:
```sh
mkdir bin
cd model

# provide input path to training data and output path to save trained model and metadata
# usage: train.py <input path> <output path> <is memory intensive (default: 0)> <pretrained weights (optional)>
./train.py ../datasets/web/training_set ../bin

# train on images pre-processed as arrays
./train.py ../datasets/web/training_features ../bin

# train with generator to avoid having to fit all the data in memory (RECOMMENDED)
./train.py ../datasets/web/training_features ../bin 1

# train on top of pretrained weights
./train.py ../datasets/web/training_features ../bin 1 ../bin/pix2code.h5
```

Generate code for batch of GUIs:
```sh
mkdir code
cd model

# generate DSL code (.gui file), the default search method is greedy
# usage: generate.py <trained weights path> <trained model name> <input image> <output path> <search method (default: greedy)>
./generate.py ../bin pix2code ../gui_screenshots ../code

# equivalent to command above
./generate.py ../bin pix2code ../gui_screenshots ../code greedy

# generate DSL code with beam search and a beam width of size 3
./generate.py ../bin pix2code ../gui_screenshots ../code 3
```

Generate code for a single GUI image:
```sh
mkdir code
cd model

# generate DSL code (.gui file), the default search method is greedy
# usage: sample.py <trained weights path> <trained model name> <input image> <output path> <search method (default: greedy)>
./sample.py ../bin pix2code ../test_gui.png ../code

# equivalent to command above
./sample.py ../bin pix2code ../test_gui.png ../code greedy

# generate DSL code with beam search and a beam width of size 3
./sample.py ../bin pix2code ../test_gui.png ../code 3
```

Compile generated code to target language:
```sh
cd compiler


# compile .gui file to HTML/CSS (Bootstrap style)
./web-compiler.py <input file path>.gui
```

