# SmolVLM2-256M NPU
![Alt text](https://github.com/user-attachments/assets/6d297a34-c516-4cb1-be4a-bca471d40fa6)
<br><br>**User**:\<image\>Describe the image.<br><br>
**Answer**: The image depicts a scene from space, specifically looking at the moon's surface. The moon is in the process of being tidied up and has been cleaned to remove any debris or stains. There are two large white objects on the right side of the image, which could be astronauts or other crew members. They appear to be working together to clean the area around the moon's surface.

The background shows a view of Earth from space, showing the planet with its atmosphere and oceans. The sky is dark, indicating that it might be either early morning or late afternoon. There are no visible clouds in the sky, which suggests that the sun is not at its peak position. The image also includes some text, but it's unclear what it says.

Overall, this image captures a moment of cleanliness and organization on the moon, with both humans and astronauts working together to clean up the area around the moon's surface.

------------

## SmolVLM2-256M VLM for RK3588 NPU (Rock 5, Orange Pi 5). <br/>
[![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://opensource.org/licenses/BSD-3-Clause)<br/><br/>
Paper: https://huggingface.co/blog/smolvlm2
Hugging face: https://huggingface.co/blog/smolvlm2

------------

## Introduction

LLMs (Large Language Models) are neural networks trained on extensive text datasets to comprehend and produce language.<br>
VLMs (Vision-Language Models) incorporate a visual encoder, allowing the model to process images and text simultaneously.<br> 
A combined VLM+LLM system is often referred to as a multimodal model.

These models can be large—hundreds of millions to billions of parameters—which impacts accuracy, memory use, and runtime speed.<br>
On edge devices like the RK3588, available RAM and compute are limited, and even the NPU has strict constraints on supported operations.<br>
Because of this, models typically need to be quantised or simplified to fit.

Performance is usually expressed in tokens (words) per second.<br>
Once converted to RKNN, parts of the model can run on the NPU, improving speed.<br>

------------

## Model performance benchmark (FPS)

All models, with C++ examples, can be found on the Q-engineering GitHub.<br><br>
All LLM models are quantized to **w8a8**, while the VLM vision encoders use **fp16**.<br>

| model         | RAM (GB)<sup>1</sup> | llm cold sec<sup>2</sup> | llm warm sec<sup>3</sup> | vlm cold sec<sup>2</sup> | vlm warm sec<sup>3</sup> | Resolution | Tokens/s |
| --------------| :--: | :-----: | :-----: | :--------: | :-----: | :--------:  | :--------: |
| [Qwen3-2B](https://github.com/Qengineering/Qwen3-VL-2B-NPU) | 3.1 | 21.9 | 2.6 | 10.0  | 0.9 | 448 x 448 | 11.5 |
| [Qwen3-4B](https://github.com/Qengineering/Qwen3-VL-4B-NPU) | 8.7 | 49.6 | 5.6 | 10.6  | 1.1 | 448 x 448 | 5.7 |
| [InternVL3.5-1B](https://github.com/Qengineering/InternVL3.5-1B-NPU) | 8.8 |  92 |   8.0 | 50.5    | 5.8 | 448 x 448 | 3.5 |
| [InternVL3.5-2B](https://github.com/Qengineering/InternVL3.5-2B-NPU) | 5.4 |  50 |   8.0 | 5.9    | 0.8 | 448 x 448 | 5 |
| [InternVL3.5-4B](https://github.com/Qengineering/InternVL3.5-4B-NPU) | 3.0 |  22 |   8.0 | 2.7    | 0.8 | 448 x 448 | 11.2 |
| [InternVL3.5-8B](https://github.com/Qengineering/InternVL3.5-8B-NPU) | 1.9 |  8.3 |   8.0 | 1.5    | 0.8 | 448 x 448 | 24 |
| [Qwen2.5-3B](https://github.com/Qengineering/Qwen2.5-VL-3B-NPU) | 4.8 | 48.3 |  4.0 | 17.9  | 1.8 | 392 x 392 | 7.0 |
| [Qwen2-7B](https://github.com/Qengineering/Qwen2-VL-7B-NPU) | 8.7 | 86.6 |   34.5 | 37.1  | 20.7 | 392 x 392 | 3.7 |
| [Qwen2-2.2B](https://github.com/Qengineering/Qwen2-VL-2B-NPU) | 3.3 | 29.1 |   2.5 | 17.1  | 1.7 | 392 x 392 | 12.5 |
| [InternVL3-1B](https://github.com/Qengineering/InternVL3-NPU) | 1.3 |  6.8 |   1.1 | 7.8    | 0.75 | 448 x 448 | 30 |
| [SmolVLM2-2.2B](https://github.com/Qengineering/SmolVLM2-2B-NPU) | 3.4 | 21.2 |   2.6 | 10.5   | 0.9  | 384 x 384 | 11 |
| [SmolVLM2-500M](https://github.com/Qengineering/SmolVLM2-500M-NPU) | 0.8 |  4.8 |   0.7 | 2.5    | 0.25 | 384 x 384 | 31 |
| [SmolVLM2-256M](https://github.com/Qengineering/SmolVLM2-256M-NPU) | 0.5 |  1.1 |   0.4 | 2.5    | 0.25 | 384 x 384 | 54 |

<sup>1</sup> The total used memory; LLM plus the VLM. <br>
<sup>2</sup> When an llm/vlm model is loaded for the first time from your disk to RAM or NPU, it is called a cold start.<br>
The duration depends on your OS, I/O transfer rate, and memory mapping.<br> 
<sup>3</sup> Subsequent loading (warm start) takes advantage of the already mapped data in RAM. Mostly, only a few pointers need to be restored.<br><br>
<img width="600" height="450" alt="Plot_1" src="https://github.com/user-attachments/assets/2dde8d27-c8ae-474c-b845-4ed52bdc0785" /><br>
<img width="600" height="450" alt="Plot_2" src="https://github.com/user-attachments/assets/0cf946d5-5458-4166-bc2b-fa1592ae4d6b" />

------------

## Dependencies.
To run the application, you have to:
- OpenCV 64-bit installed.
- rkllm library.
- rknn library.
- Optional: Code::Blocks. (```$ sudo apt-get install codeblocks```)

### Installing the dependencies.
Start with the usual 
```
$ sudo apt-get update 
$ sudo apt-get upgrade
$ sudo apt-get install cmake wget curl
```
#### OpenCV
To install OpenCV on your SBC, follow the Raspberry Pi 4 [guide](https://qengineering.eu/install-opencv-on-raspberry-64-os.html).<br><br>
Or, when you have no intentions to program code:
```
$ sudo apt-get install lib-opencv-dev 
```
------------

## Installing the app.
```
$ git clone https://github.com/Qengineering/SmolVLM2-256M-NPU.git
```

#### RKLLM, RKNN
To run SmolVLM2-500M, you need to have the **rkllm-runtime** library version **1.2.2** (or higher) installed, as well as the **rknpu driver** version **0.9.8**.<br>
If you don't have these on your machine, or if you have a lower version, you need to install them.<br>
We have provided the correct versions in the repo.<br>
```bash
$ cd ./SmolVLM2-256M-NPU/aarch64/library
$ sudo cp ./*.so /usr/local/lib
$ cd ./SmolVLM2-256M-NPU/aarch64/include
$ sudo cp ./*.h /usr/local/include
```
### Download the LLM and VLM model.
The next step is downloading the models.<br>
Download the two files (400 MB) from our Sync.com server:<br>
[smolvlm2-256m-video-instruct_w8a8_rk3588.rkllm](https://ln5.sync.com/dl/9af47c990#nhnm5iny-v4d7b8nz-5dfu3jcm-4cjsvnwi) and [smolvlm2_256m_vision_fp16_rk3588.rknn](https://ln5.sync.com/dl/3fa069520#ii5uprqg-na2e48sp-zs6t4qn2-dbyva2ae)<br>
Copy both to your `./model` folder.

### Building the app.
Once you have the two models, it is time to build your application.<br>
You can use **Code::Blocks**.
- Load the project file *.cbp in Code::Blocks.
- Select _Release_, not Debug.
- Compile and run with F9.
- You can alter command line arguments with _Project -> Set programs arguments..._ 

Or use **Cmake**.
```
$ mkdir build
$ cd build
$ cmake ..
$ make -j4
```
### Running the app.
The app has the following arguments.
```bash
VLM_NPU Picture RKNN_model RKLLM_model NewTokens ContextLength
```
| Argument   | Comment |
| --------------| --  |
| picture | The image. Provide a dummy if you don't want to use an image | 
| RKNN_model | The visual encoder model (vlm) | 
| RKLLM_model | The large language model (llm) | 
| NewTokens | This sets the maximum number of new tokens. Optional, default 2048| 
| ContextLength | This specifies the maximum total number of tokens the model can process. Optional, default 4096| 

<br>In the context of the Rockchip RK3588 LLM (Large Language Model) library, the parameters NewTokens and ContextLength both control different limits for text generation, and they're typical in LLM workflows.<br>
**NewTokens**<br> 
This sets the maximum number of tokens (pieces of text, typically sub-word units) that the model is allowed to generate in response to a prompt during a single inference round. For example, if set to 300, the model will not return more than 300 tokens as output, regardless of the prompt length. It's important for controlling generation length to avoid too-short or too-long responses, helping manage resource use and output size.<br>
**ContextLength**<br>
This specifies the maximum total number of tokens the model can process in one go, which includes both the prompt (input) tokens and all generated tokens. For example, if set to 2048 and your prompt already uses 500 tokens, the model can generate up to 2048-500 = 1548 new tokens. This is a hardware and architecture constraint set during model conversion and deployment, as the context window cannot exceed the model's design limit (for instance, 4096 or 8192 tokens depending on the model variant).

A typical command line can be:
```bash
VLM_NPU ./Moon.jpg ./models/smolvlm2_256m_vision_fp16_rk3588.rknn ./models/smolvlm2-256m-video-instruct_w8a8_rk3588.rkllm 2048 4096
```
The NewTokens (2048) and ContextLength (4096) are optional and can be omitted.
### Using the app.
Using the application is simple. Once you provide the image and the models, you can ask everything you want.<br>
Remember, we are on a bare Rock5C with a tiny model, so don't expect the same quality answers as ChatGPT can provide.<br>
If you want more realistic and sensible output, you'd better use a larger model like [InternVL3](https://github.com/Qengineering/InternVL3-NPU) or [Qwen](https://github.com/Qengineering/Qwen2-NPU).<br>
If you like the response to include a discussion of the picture, include the `<image>` token once in your prompt.<br>
The app remembers the dialogue until you give the token `<clear>`.<br>
With `<exit>`, you leave the application.
### C++ code.  
Below, you find the surprisingly little code of main.cpp. 
```cpp
#include "RK35llm.h"

int main(int argc, char** argv)
{
    std::string input_str;
    std::string output_str;
    RK35llm RKLLM;

    RKLLM.SetInfo(true);            //yes, you may give me additional model information
    RKLLM.SetSilence(false);        //you may print the incremental text chunks on the terminal

    if     (argc< 4) {std::cerr << "Usage: " << argv[0] << " image vlm_model llm_model [option]NewTokens [option]ContextLength\n"; return -1;}
    else if(argc==4) RKLLM.LoadModel(argv[2],argv[3]);
    else if(argc==5) RKLLM.LoadModel(argv[2],argv[3],std::atoi(argv[4]));
    else if(argc> 5) RKLLM.LoadModel(argv[2],argv[3],std::atoi(argv[4]),std::atoi(argv[5]));

    cv::Mat Pic = cv::imread(argv[1]);
    RKLLM.LoadImage(Pic);

    while(true) {
        printf("\n");
        printf("User: ");

        std::getline(std::cin, input_str);
        if (input_str == "exit") break;

        output_str = RKLLM.Ask(input_str);
//        std::cout << "\nLLM Reply: " << output_str << std::endl;
    }

    return 0;
}
```
Most code speaks for itself. One remark.<br>
The LLM generates the answer to your request in little pieces of text. You see them as if the LLM is typing on your terminal.<br>
RKLLM.SetSilence controls this behaviour. When set to `true`, it will suppress this output. RKLLM.Ask() also returns the complete answer.<br>
In main.cpp uncomment this line:
```cpp
std::cout << "\nLLM Answer: " << output_str << std::endl;
```

------------

## VLM examples.
| Image         | Dialog |
| --------------| --  |
| ![Singapore_S](https://github.com/user-attachments/assets/e742059a-3bdb-41be-9647-e7767da12ce6) |**User**: \<image\>Which city is this?<br>**Answer**: This image shows the reflection of the city lights on the water, creating a beautiful and serene scene. The reflections are so clear that they can be seen from any direction. The reflection of the city lights is not just a reflection but also a visual representation of the city's architecture, design, and history. It's a unique and captivating sight that adds to the overall beauty of the image.|
| ![ChineseWall_S](https://github.com/user-attachments/assets/3ba4b57a-4b22-4050-b995-9599c6d239de) |**User**: \<image\>Tell me something about the history of this place.<br>**Answer**: The ancient city of Xi'an, also known as the Forbidden City in Beijing, is one of the most famous and significant historical sites in China. It was built by the Ming dynasty in the 15th century and remains a popular tourist destination for its rich history, stunning architecture, and cultural significance. Here are some key facts about Xi'an:<br>**History:**<br>* The Forbidden City was built as a royal palace complex, originally intended to house the emperor's family and other officials. It was designed by the Ming dynasty's architect, Zhengde, and completed in 1629.<br>* The palace complex was divided into four main sections: the north section, south section, east section, and west section.<br>* The north section featured a large courtyard with a grand staircase leading to the top of the palace, which was decorated with intricate carvings and sculptures.<br>* The south section featured a smaller courtyard with a more modest layout, while the east section featured a larger courtyard with a more elaborate design.<br>* The west section featured a smaller courtyard with a more traditional design, featuring a grand staircase leading to the top of the palace.<br><br>**Architecture:**<br>* The Forbidden City is famous for its stunning architecture, including:<br>+ The Great Hall of the People (1426-1908), one of the largest and most impressive buildings in the world.<br>+ The Temple of Heaven (1537-1912), a large temple complex that was built to house the emperor's family.<br>+ The Great Wall of China (1604-1912), which is one of the longest and most impressive walls in the world.<br>* The palace complex features numerous grand buildings, including:<br>+ The Great Hall of the People (1426-1908), one of the largest and most impressive buildings in the world.<br>+ The Temple of Heaven (1537-1912), a large temple complex that was built to house the emperor's family.<br>+ The Great Wall of China (1604-1912), which is one of the longest and most impressive walls in the world.|
| ![Pizza_S](https://github.com/user-attachments/assets/26fd5da3-f491-4b48-978a-14eae0819247) |**User**: \<image\>What are the ingredients I need to make this?<br>**Answer**:Answer: To make this pizza, you will need:<br>1. **Pizza dough**: A thick, golden-brown crust that will hold up well during cooking. You can use a traditional pizza dough or a dough made from flour and water.<br>2. **Vegetables**: You'll need to add some fresh basil leaves for color and flavor. You can use fresh basil in the form of sprigs or chopped leaves, or you can use dried basil if you prefer.<br>3. **Pizza sauce**: A thick, red sauce that will help the pizza cook evenly. You can use a traditional tomato sauce or a marinara sauce.<br>4. **Flour**: You'll need some flour to make the dough. You can use a dry-roasted or whole wheat flour.<br>5. **Water**: You'll need water for cooking and mixing the ingredients.<br>6. **Pizza peel**: A thin, flat piece of parchment paper that will help you roll out the pizza dough.<br>7. **Pizza cutter**: A sharp knife to cut into the dough.<br><br>Remember to let the dough rest for at least 30 minutes before rolling it out to ensure it is evenly cooked and has a good texture. You can also use a pizza peel to help flatten the dough if you're using a traditional method.<br><br>Enjoy your delicious homemade pizza!<br><br>**User**: What is the first step?<br>**Answer**: The first step in making a pizza is to prepare the dough by kneading it until it becomes smooth and elastic. This will help ensure that the crust is well-formed and that the sauce can be easily spread out during cooking. Once the dough is ready, you can start rolling it out into a thin layer on a pizza peel or a baking sheet.<br><br>Remember to let the dough rest for at least 30 minutes before rolling it out to ensure it is evenly cooked and has a good texture. This will help prevent the sauce from sticking to the crust during cooking.|
| ![Titanic_S](https://github.com/user-attachments/assets/bf6da76c-c9a4-41c6-bea1-2b4411dd4013) |**User**: \<image\>Which movie is this?<br>**Answer**: Answer: This question seems to be asking for information about the movie "The Nightmare Before Christmas" by Steven Spielberg, specifically focusing on the movie's title and its ending. However, I cannot provide a direct answer as the movie itself is not mentioned in the given context. The provided text does not mention any specific movie or its ending.<br><br>Please provide more information about the movie to help me better understand it.|

## LLM example.
**User**: Can you make me a C++ call that embeds the Pythagorean theorem?<br><br>
**Answer**:Here's a simple C++ function that does what you're asking for:
```cpp
#include <iostream>
using namespace std;

int main() {
    int x = 10, y = 5;

    cout << "The square of the number is: " << x * x << endl;
    cout << "The sum of the numbers is: " << (x + y) / 2 << endl;
    cout << "The product of the numbers is: " << (x * y) / 2 << endl;

    return 0;
}
```
This function takes an integer `x` and a single integer `y`, calculates their squares, sums them up, and then divides by two to get the product. The result is printed out as well. Note that this function does not handle negative numbers or zero correctly. If you want to handle these cases, you'll need to modify the function accordingly.

User: Can you make me a C++ call that embeds the Pythagorean theorem?
Answer: Here's a simple C++ function to calculate the square root of a given number:
```cpp
#include <iostream>
using namespace std;

int main() {
    int n = 10; // number of sides

    double x = sqrt(n); // square root

    cout << "The square root of " << n << " is " << x << endl;

    return 0;
}
```
This code defines a function `sqrt` that calculates the square root of a given number. The `main` function demonstrates how to use this function with an example.
Note: This code uses the `std::sqrt` function from the `<cmath>` library, which is a standard C++ library for calculating the square root of a number. If you're using a different library or framework, you may need to adjust the code accordingly.

------------

## **[Rock5GPT](https://rock5gpt.qengineering.eu)**
To get a taste, try our professional Qwen3 AI-chatbot running on a Rock 5C: https://rock5gpt.qengineering.eu
<img width="815" height="1151" alt="Rock5GPT" src="https://github.com/user-attachments/assets/3ce5ad31-bc2b-4513-8ac9-42be793a86db" /><br>

------------

[![paypal](https://qengineering.eu/images/TipJarSmall4.png)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=CPZTM5BB3FCYL) 

------------
## Appendix.
Porting the vision module of SmolVLM2 to rknn requires modifications to the intermediate ONNX file.<br>
Specifically, the indices for the Gather operation must be converted from a floating-point (FP16) data type to an integer. Please refer to the following image for an example.<br>


<img width="325" height="285" alt="Screenshot from 2025-10-28 10-19-46" src="https://github.com/user-attachments/assets/e52075bd-b800-4fac-92b9-feea5f1e18fb" />
<img width="325" height="285" alt="Screenshot from 2025-10-28 10-20-45" src="https://github.com/user-attachments/assets/621ec0ba-f5d7-451b-beb2-3a6dc2cdf08c" />
