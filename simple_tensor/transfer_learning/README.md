# README #
```diff
+ UNDER DEVELOPMENT
```
### NEWS
| Date       |                                                         News                                                                     |     Version       |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------- | ----------------- |
|3rd Jan 2019 | Inception V4 added |       > v0.0.2           |
|28th march 2019 | Densenet 121 added |       > v0.4.1         |



### ABOUT PROJECT
This is a project for tensorflow transfer learning simplification

### DEPENDENCIES
1. Tensorflow 
2. simple_tensor

### MODELS
#### :shipit: Available Model
1. Inception V4 [pretrained model](http://download.tensorflow.org/models/inception_v4_2016_09_09.tar.gz)
2. Densenet 121 [pretrained model](https://drive.google.com/open?id=0B_fUSpodN0t0eW1sVk1aeWREaDA)

#### :shipit: Model Performance
| Model Name               |                  Dataset                   |   Top 1 accuracy  |  Top 5 accuracy   |
| ------------------------ | ------------------------------------------ | ----------------- |-------------------|
| Inception V4             |                 Imagenet                   |         80.2      |        95.3       |
| DEnsenet 121             |                 Imagenet                   |         74.91     |        93.8       |


### FOLDER STRUCTURE
- A. Training   
---->> **dataset path**    
--------------->> **class 1 folder**  
------------------------------>> a.jpg    
------------------------------>> ...  
------------------------------>> axxx.jpg         
...     
--------------->> **class n folder**    
------------------------------>> a.jpg  
------------------------------>> ...    
------------------------------>> axxx.jpg


### :shipit: DENSENET Usage Example
#### Training
```python
import tensorflow as tf
from simple_tensor.tensor_losses import softmax_crosentropy_mean
from simple_tensor.transfer_learning.image_recognition import *

# ------------------------- #
# build network and loss    #
# ------------------------- #
imrec = ImageRecognition(classes=['...', '..'],
                         dataset_folder_path = 'path to your dataset/', 
                         input_height = 300,
                         input_width = 300, 
                         input_channel = 3)

out, var_list = imrec.build_densenet_base(imrec.input_placeholder,
                                    dropout_rate = 0.15,
                                    is_training = True,
                                    top_layer_depth = 128)
cost = softmax_crosentropy_mean(out, imrec.output_placeholder)

# ------------------------- #
# optimizer, saver, and session   
# ------------------------- #
update_ops = tf.get_collection(tf.GraphKeys.UPDATE_OPS)
with tf.control_dependencies(update_ops):
    optimizer = tf.train.AdamOptimizer(learning_rate=0.0001).minimize(cost)

saver_partial = tf.train.Saver(var_list)
saver_all = tf.train.Saver()
session = tf.Session()
session.run(tf.global_variables_initializer())
# >> for the first training
saver_partial.restore(sess=session, save_path='/home/model/tf-densenet121/tf-densenet121.ckpt')
# >> for continuing your training
# saver_all.restore(sess=session, save_path='your model path from previous training')

# ------------------------- #
# optimize                  #  
# ------------------------- #
imrec.optimize(iteration=2000, 
         subdivition=1,
         cost_tensor=cost,
         optimizer_tensor=optimizer,
         out_tensor = out,
         session = session, 
         saver = saver_all,
         train_batch_size=2, 
         val_batch_size=10,
         path_tosave_model='model/model1')

```


### :shipit: Inception-V4 Usage Example
#### Training
```python
import tensorflow as 
from simple_tensor.tensor_losses import softmax_crosentropy_mean
from simple_tensor.transfer_learning.image_recognition import *


# ------------------------- #
# build network and loss    #
# ------------------------- #
imrec = ImageRecognition(classes=['...', '..'],
                         dataset_folder_path = 'path to your dataset/', 
                         input_height = 300,
                         input_width = 300, 
                         input_channel = 3)

is_training = False # always set it to false during training or inferencing (bug in inceptionv4 base tf slim)
out, var_list = imrec.build_inceptionv4_basenet(imrec.input_placeholder, 
                                                is_training = is_training, 
                                                final_endpoint='Mixed_6a', # 'Mixed_6a, Mixed_5a, Mixed_7a
                                                top_layer_depth = 256)
cost = softmax_crosentropy_mean(out, imrec.output_placeholder)

# ------------------------- #
# optimizer, saver, and session   
# ------------------------- #
update_ops = tf.get_collection(tf.GraphKeys.UPDATE_OPS)
with tf.control_dependencies(update_ops):
    optimizer = tf.train.AdamOptimizer(learning_rate=0.0001).minimize(cost)

saver_partial = tf.train.Saver(var_list)
saver_all = tf.train.Saver()
session = tf.Session()
session.run(tf.global_variables_initializer())
# >> for the first training
saver_partial.restore(sess=session, save_path='/home/model/tf-densenet121/tf-densenet121.ckpt')
# >> for continuing your training
# saver_all.restore(sess=session, save_path='your model path from previous training')

# ------------------------- #
# optimize                  #  
# ------------------------- #
imrec.optimize(iteration=2000, 
         subdivition=1,
         cost_tensor=cost,
         optimizer_tensor=optimizer,
         out_tensor = out,
         session = session, 
         saver = saver_all,
         train_batch_size=2, 
         val_batch_size=10,
         path_tosave_model='model/model1')

```
