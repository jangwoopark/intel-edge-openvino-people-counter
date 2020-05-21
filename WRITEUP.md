# Project Write-Up

You can use this document as a template for providing your project write-up. However, if you
have a different format you prefer, feel free to use it as long as you answer all required
questions.

## Explaining Custom Layers

The process behind converting custom layers involves:

Building the model.
Creating the Custom Layer.
Using Model Optimizer to Generate IR Files Containing the Custom Layer.
Generating the Model IR Files.
Inference Engine Custom Layer Implementation for the Intel CPU.

Some of the potential reasons for handling custom layers are the following:

The Model Optimizer searches for each layer of the input model in the list of known layers before building the model's internal representation, optimizing the model, and producing the Intermediate Representation. The list of known layers is different for each of supported frameworks. To see the layers supported by the framework, one has to refer to the corresponding section. Custom layers are layers that are not included into a list of known layers. If the topology contains any layers that are not in the list of known layers, the Model Optimizer classifies them as custom. This allows flexibility to design and write our own custom layer which can be converted into IR for inferencing.

## Comparing Model Performance

My method(s) to compare models before and after conversion to Intermediate Representations were scripted in two python files: pre.py and post.py which can be run from the command line. The results are as follows:

faster_rcnn_inception_v2_coco_2018_01_28

pre model
latency: 1.29E+03 (average in ms)
memory: 562 im mb

post model
latency: 9.44E+02 (average in ms)
memory: 281 in mb

The size of the model pre- and post-conversion was measured by comparing the .pb and the .bin and .xml files for the frozen_inference_graph in the following way:

faster_rcnn_inception_v2_coco_2018_01_28
pre: 57.2 MB post: 53.2 MB + 126.4 kb

The inference time of the model pre- and post-conversion was given in the pre and post results above. faster_rcnn_inception latency improved by 36% respectively with no loss of accuracy.

Differences in Cloud and Edge computing: Cloud Computing is more suitable for projects and organizations which deal with massive data storage. Cloud processing power is nearly limitless. Any analysis tool can be deployed at any time. Results may need to be widely distributed and viewed on a variety of platforms. The cloud can be accessed from anywhere on multiple devices. The form factor and environmental limits of some applications could increase the cost of edge computing and make the cloud more cost-effective. Edge Computing is regarded as ideal for operations with extreme latency concerns. Thus, medium scale companies that have budget limitations can use edge computing to save financial resources. It is a better option when there is not an enough or reliable network bandwidth to send the data to the cloud. It works when the communication network’s connection to the cloud is not robust or reliable enough to be dependable. Applications need rapid data sampling or must calculate results with a minimum amount of delay. Edge Computing requires a robust security plan including advanced authentication methods and proactively tackling attacks.

## Assess Model Use Cases

Some of the potential use cases of the people counter app are capture and record information on the number of people for security reasons, track activity in retail or departmental store or any space with human traffic such as factory work spaces and building entrances for human and other activities. 

Each of these use cases would be useful because, for security reasons, it can track if people gather in some space of retail stores where less crowd is expected, for example in the billing lane or in the back of the store. Each of these cases would be useful because some of the sentivity areas of factory or building requires continous monitoring and survillance; and, to gather stats on people visiting some facilities. This data can be further used for various purpose.

## Assess Effects on End User Needs

Lighting, model accuracy, and camera focal length/image size have different effects on a
deployed edge model. The potential effects of each of these are as follows...

Areas with low lighting condition may require low confidence threhold with higher precision to capture people in frames.

If the camera is mounted in a market place or near a traffic stop then the accuracy precision along with its low confidence threshold is set to capture as many people as possible in a moving frame. High capacity edge hardware devices with good memory may suffice such requirements.

If the camera is mounted close to area being monitored where proximity is less then low accuracy model deployed on edge device may be good enough for good inference. Low capacity hardware might suffice this requirement.

If the camera is mounted high enough at top of the building then its proximity is far away and its focal length should be adjusted to capture large areas. For such applications high accuracy is desired to distingish people from other objects. Low capacity edge hardware with good memory may suffice such requirement as higher precision is desired.



## Model Research

[This heading is only required if a suitable model was not found after trying out at least three
different models. However, you may also use this heading to detail how you converted 
a successful model.]

In investigating potential people counter models, I tried the following model:

wget http://download.tensorflow.org/models/object_detection/faster_rcnn_inception_v2_coco_2018_01_28.tar.gz

python /opt/intel/openvino/deployment_tools/model_optimizer/mo.py --input_model faster_rcnn_inception_v2_coco_2018_01_28/frozen_inference_graph.pb --tensorflow_object_detection_api_pipeline_config faster_rcnn_inception_v2_coco_2018_01_28/pipeline.config --reverse_input_channels --tensorflow_use_custom_operations_config /opt/intel/openvino/deployment_tools/model_optimizer/extensions/front/tf/faster_rcnn_support.json -o faster_rcnn_inception_v2_coco_2018_01_28

For running the main.py file use the following parameters:
python main.py

-m your-model.xml (mine is input_model faster_rcnn_inception_v2_coco_2018_01_28/frozen_inference_graph.xml)

-l /opt/intel/openvino/deployment_tools/inference_engine/lib/intel64/libcpu_extension_sse4.so 

-d CPU

-pt 0.4 (not 0.6 like in the Guide)

| ffmpeg 

-v warning

-f rawvideo

-pixel_format bgr24

-video_size 758x432 (not 768x432 like in the Guide)

-framerate 24

-i - http://0.0.0.0:3004/fac.ffm
