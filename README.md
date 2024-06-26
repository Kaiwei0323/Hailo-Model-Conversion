# Hailo Model Convertion

## Description
Convert an ONNX model to a Hailo AI Runtime (HAR) format for execution on HailoRT

## Hardware Requirement
* NVIDIA GeForce RTX 4070
* RAM 16 GB

## Software Requirement
* Hailo AI SW Suite 2024-04
* Ubuntu 22.04
* GPU-Driver 552
* python 3.10
* Virtualenv

## Hailo Environment Setup
### Hailo AI Suite Installation
```
./hailo_ai_sw_suite_2024-04.run
```
### Activate Virtual Environment
```
source hailo_ai_sw_suite/hailo_venv/bin/activate
```
### Run Jupyter Notebook
```
jupyter notebook
```

## Parameter Modification
### End Node Names
```
end_node_names = [
    '/0/model.22/cv2.0/cv2.0.2/Conv',
    '/0/model.22/cv3.0/cv3.0.2/Conv',
    '/0/model.22/cv2.1/cv2.1.2/Conv',
    '/0/model.22/cv3.1/cv3.1.2/Conv',
    '/0/model.22/cv2.2/cv2.2.2/Conv',
    '/0/model.22/cv3.2/cv3.2.2/Conv'
]
```
__Reference:__ [yolov8m.yaml](https://github.com/hailo-ai/hailo_model_zoo/blob/master/hailo_model_zoo/cfg/networks/yolov8m.yaml)

### Model Script Commands
```
model_script_commands = [
    'normalization1 = normalization([0.0, 0.0, 0.0], [255.0, 255.0, 255.0])\n',
    'model_optimization_config(calibration, batch_size=2)\n',
    'change_output_activation(conv58, sigmoid)\n',
    'change_output_activation(conv71, sigmoid)\n',
    'change_output_activation(conv83, sigmoid)\n',
    'nms_postprocess("postprocess_config/yolov8m_nms_config.json", meta_arch=yolov8, engine=cpu)\n',
    'post_quantization_optimization(finetune, policy=enabled, learning_rate=0.000025)\n',
]
```
__Reference:__ [yolov8m.alls](https://github.com/hailo-ai/hailo_model_zoo/blob/master/hailo_model_zoo/cfg/alls/generic/yolov8m.alls)

### Model NMS Configure
```
"classes": 2,
...
"bbox_decoders": [
  {
    "name": "model/bbox_decoder57",
    "stride": 8,
    "reg_layer": "model/conv57",
    "cls_layer": "model/conv58"
  },
  {
    "name": "model/bbox_decoder70",
    "stride": 16,
    "reg_layer": "model/conv70",
    "cls_layer": "model/conv71"
  },
  {
    "name": "model/bbox_decoder82",
    "stride": 32,
    "reg_layer": "model/conv82",
    "cls_layer": "model/conv83"
  }
]
```
__Reference:__ [yolov8m_nms_config.json](https://github.com/hailo-ai/hailo_model_zoo/blob/master/hailo_model_zoo/cfg/postprocess_config/yolov8m_nms_config.json)

## ONNX Model
```
best.onnx
Accuracy: 96.1 %
```

## Hailo AI Runtime Model
### Parsed model
```
yolov8m_hailo_model.har
```

### Optimized model
```
best_quantized_model.har
```

## Reference
* [Hailo Parsing Tutorial](https://hailo.ai/developer-zone/documentation/dataflow-compiler-v3-27-0/?sp_referrer=tutorials_notebooks/notebooks/DFC_1_Parsing_Tutorial.html#Parsing-Example-from-ONNX-to-HAR)
