![IMG_2684.jpg](https://usercdn.edgeimpulse.com/project149095/64cacaf7203218dbaeaecdc9f13c7a5ff03aa5eb3632b740890ffd890644111f)
# Story     
The direct annual cost of vandalism runs in billions of dollars annually in the United States only. Breaking glass and defacing properties are some of the serious forms of vandalism. Conventional security techniques such as direct lighting and intruder alarms are ineffective in many locations and cases. So by means of this project, we are able to detect the sound of glass breaking and can alert the user instantly about the event. 
In this project, we mainly focus on vandal activity such as glass breaking, however, this project can be applied to any vandal activity that produces sounds.
# How Does It Work
The device will work as follows, suppose a vandal tried to break the glass then the tinyML algorithm running in the device can recognise the event using a microphone unit. Then the device will give mail notifications to the registered users regarding the vandalism detection.
![Visual Studio Code.lnk.jpg](https://usercdn.edgeimpulse.com/project149095/fdfb3c8bd56330c108ac60abc2167f6fc5e92b8235591310d4f5abec0e3018d0)
# Hardware   
### Arduino Nano 33 BLE Sense  
![Nano 33 BLE sense.jpg](https://usercdn.edgeimpulse.com/project149095/eab4cd400312284c7a788d835f26f143a8d388d2aef1e3204e1b3c8d46c8f998) 
For this project we are using, Arduino nano 33 ble sense. It's a 3.3V AI-enabled board in the smallest available form factor. It comes with a series of embedded sensors including MP34DT05 (Digital Microphone). 
### ESP -01   
![Esp 01.jpg](https://usercdn.edgeimpulse.com/project149095/9985f6574e6532e512083441a5cc2a9a778a62567a10904bc543b12f4e7767a9)
The ESP-01 is used for adding the wifi capability for the device because the Arduino nano 33 ble sense has only Bluetooth. Actually, the wifi is used for sending mail alerts. The serial communication is established between the Arduino and esp 01 for giving the mail alert.   

# Software    
  
## Data acquistion   
One of the most important parts of any machine learning model is its dataset. The edge impulse offers us two options to create our dataset either direct uploading the file or recording via the device itself. Here we proceeded with recording the device itself because the first reason is that it's just a prototype so the data will be limited and the second reason is that it produces more accuracy. For connecting the BLE sense to the edge impulse please read this [tutorial](https://docs.edgeimpulse.com/docs/development-platforms/officially-supported-mcu-targets/arduino-nano-33-ble-sense).    
In this scenario, we have only two classes **Glass break** and **Noise**. Glass breaking sounds that we have used are from the vivid online resources and the major noise datasets are from this **Microsoft Scalable Noisy Speech Dataset (MS-SNSD)**. We also included the natural noise in the room apart from **MS-SNSD**.   
The sound recording was done for **20** seconds at **16KHz** sampling rate. Something to keep in mind is that you must keep the sampling rate the same between your training dataset and your deployment device. When you are planning to use the **44.1Khz** sound, you should need to downsample it to the 16KHz(when you are going to deploy in Arduino).   
We collected around 10 minutes of data and split it between training and test set. In the training data we split the sample to **2s** otherwise, the inferencing will fail because the BLE sense has limited memory to handle the data. 
![data_acquistion.jpg](https://usercdn.edgeimpulse.com/project149095/3650d812a6a423c34f8b0ace37b11d208d590cbc136e6d264d831e8d002d7171)       
     
## Impulse Design    
This is our Impulse. Impulse is actually the machine learning pipeline termed by the Edge Impulse.
![Impulse Design.jpg](https://usercdn.edgeimpulse.com/project149095/899493f5ad25aad36d09c7dac83284dcb1440c59b772f67afd841cc275a0b8d5)  
Here we used **MFE** as the processing block ,because it is very suitable for non-human voices. We have used default parameters of the MFE block. 

## Neural Network
These are our Neural Network settings which is most suitable for our data.If your are tinkering with your own dataset you should need to change this parameters if required. 
![Neural Network settings.jpg](https://usercdn.edgeimpulse.com/project149095/0a5c143c7828763a48a67a08f433ff3cf8385d701224bfccb19d5d7812d75e9d)
We enabled the Data augmentation which helps us to randomnly transform data during training. So by means of this we are able to run more training cycles without overfitting and also it leads to the improved accuracy.
This is our Neural Network architecture.
![NNA.jpg](https://usercdn.edgeimpulse.com/project149095/cee3a813e18e40dabc30c3350c4d027626600eec1665b89252b0bbb328defec6)
We have used the default 1D convolutional.
Then we trained the model and we got 97% accuracy,which is very awesome. By looking the confusion matrix it is clear that there is no sign of underfitting and overfitting.
![Model training output.jpg](https://usercdn.edgeimpulse.com/project149095/a4aa46879b18ff2b150b71213a7a0244a54420df757683ff97f75e816335fdca)  

## Model Testing   
Before deploying the model it's a good practice to run the inference on the test dataset. In the model testing, we got around 92% accuracy.
![Model testing.jpg](https://usercdn.edgeimpulse.com/project149095/6213a1ee7e417926ced6be49ad78d2938f121fbc2e8ca99e474e90476eab6ede)    
Let's look into some of the misclassifications,         
In this case, the noise very well resembled the Glass break that's why it is misclassified.
![misclassification1.jpg](https://usercdn.edgeimpulse.com/project149095/01543564b153a5b7b9d9d10c0959685ce90a7e522a41b357f76f0217c0fae8fc)
In this case too, the model performed very well in classifying the data, the data contains both the Glass_Break and the noise but the majority of the data was noise, that's why its misclassified.
![mis2.jpg](https://usercdn.edgeimpulse.com/project149095/bac644fa20c3e0e6f95954664bff3a00eab0b0f0cfb7d4fb398e5e655f02e535)
In these two cases(below shown) also the noise was major.    
![mis3.jpg](https://usercdn.edgeimpulse.com/project149095/1b5aad086d040e815b4b66a7b949fe473c36b03502c48d9144e791d3632d2349)   
![mis4.jpg](https://usercdn.edgeimpulse.com/project149095/15691d654d60cb611ef4b5fd7326f3873a500b16d7eb85ae174187a664368fe9)   
The model is performing very well and can be deployed it in the real world.

## Deployment
For deploying the impulse to BLE Sense,we created a arduino library with this optimizations.  
![deploy.jpg](https://usercdn.edgeimpulse.com/project149095/27cb1abb3cd6d892add9a796e3b6b76e4873cefc863fa7c6880fab2532fe125a)
Then we add that library to the Arduino IDE. Then we modified the example sketch that is provided. You can find entire code and assets including circuit in this [Github repo](https://github.com/CodersCafeTech/Vandalism-Detection).
## IFTT   
![IFTTT.png](https://usercdn.edgeimpulse.com/project149095/7f2cfa0ca11ce3cd73bf217876c8dcb628276a6bc206171ab55984f30bae5347)   
For triggering the mail we used IFTT service. To setup the mail triggering upon any poitive detections please have a look at this [tutorial](https://www.youtube.com/watch?v=MXqWt7oK4JY).   
This is the applet created by me.
![applet.jpg](https://usercdn.edgeimpulse.com/project149095/8f5b19ede5f445b004ee1d2bf73357bfdd8fd6ffa6960723ca26dbd1fff67994)
## Case
All the components were fitted inside this case.
![IMG_2680.jpg](https://usercdn.edgeimpulse.com/project149095/629f80ac45ebe31aca88807ea5cae6dd4d21587c530d49dbfd01cba2c0ee322b)    

## Real world Testing     
Let's test the model after deploying it on the BLE sense board. You can check it out in the below [video](https://youtu.be/lhHwsutthis). We have actually played the Glass breaking sound via a large speaker.









