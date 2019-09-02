## Designing Effective Inter-Pixel Information Flow for Natural Image Matting:
Alphamatting is the problem of extracting the foreground from an image. Given the input of image and its corresponding, we try to extract the foreground from the background. Following is an example - 
![net image](https://github.com/muskaankularia/opencv_contrib/blob/alphamatting/modules/alphamat/img/net.png =200x155)
![net trimap](https://github.com/muskaankularia/opencv_contrib/blob/alphamatting/modules/alphamat/trimap/net.png =200x155)
![net result](https://github.com/muskaankularia/opencv_contrib/blob/alphamatting/modules/alphamat/Resule/result_net.png =200x155)


This is a pixel-affinity based alpha matting algorithm which solves a linear system of equations using preconditioned conjugate gradient method. Affinity-based methods operate by propagating opacity information from known opacity regions(K) into unknown opacity regions(U) using a variety of affinity definitions mentioned as -
* Color mixture information flow - Opacity transitions in a matte occur as a result of the original colors in the image getting mixed with each other due to transparency or intricate parts of an object. They make use of this fact by representing each pixel in U as a mixture of similarly-colored pixels and the difference is the energy term ECM,  which is to be reduced. This is coded in **cm.hpp**
* K-to-U information flow - Connections from every pixel in U to both F(foreground pixels) and B(background pixels) are made to facilitate direct information flow from known-opacity regions to even the most remote opacity-transition regions in the image. This is coded in **KtoU.hpp**
* Intra U information flow - They distribute the information inside U effectively by encouraging pixels with similar colors inside U to have similar opacity. This is coded in **intraU.hpp**
* Local information flow - Spatial connectivity is one of the main cues for information flow which is achieved by connecting each pixel in U to its immediate neighbors to ensure spatially smooth mattes. This is coded in **local_info.hpp**

Using these information flow, energy/error(E) is obtained as a weighted local composite of E<sub>CM</sub>, E<sub>KU</sub>(K-to-U information flow), E<sub>UU</sub>(Intra U information flow), E<sub>L</sub>(Local information flow).
E represents the deviation of unknown pixels opacity or colour from what we predict it to be using other pixels. So, the algorithm aims at minimizing this error. This is coded in **alphac.cpp**

Pre-processing and post-processing is implemented in **trimming.hpp**

To run the code -
1. **g++ -std=c++11 alphac.cpp \`pkg-config --cflags --libs opencv\`**
1. **./a.out \<path to image> \<path to corresponding trimap>**

Sample image and trimap are in opencv_contrib/modules/alphamat/src/img and opencv_contrib/modules/alphamat/src/trimap

Results for input_lowres are available here -
https://docs.google.com/document/d/1BJG4633_U5K-Z0QLp3RTi43q25NI0hrTw-Q4w_85NrA/edit?usp=sharing

Average time taken to compute the different flows is 40s, but solving of linear equations using preconditioned conjugate gradient method takes another 2-3 min, which can be lessened by allowing lesser iterations.
