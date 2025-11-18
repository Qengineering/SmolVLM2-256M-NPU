# InternVL3 NPU
![Alt text](https://github.com/user-attachments/assets/6d297a34-c516-4cb1-be4a-bca471d40fa6)
<br><br>**User**:\<image\>Describe the image.<br><br>
**Answer**: The image depicts an astronaut in a white space suit lounging on the moon's surface with a green bottle of soda in hand. The background features Earth from space and part of a lunar module, suggesting a surreal scene. The setting is dramatic, with the vastness of space surrounding the astronaut and the Moon itself.

------------

## InternVL3 VLM for RK3588 NPU (Rock 5, Orange Pi 5). <br/>
[![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://opensource.org/licenses/BSD-3-Clause)<br/><br/>
Paper: [InternVL3: Exploring Advanced Training and Test-Time Recipes for Open-Source Multimodal Models](https://arxiv.org/pdf/2504.10479)<br/><br/>
Hugging face: https://huggingface.co/OpenGVLab/InternVL3-78B

------------

## Introduction

LLMs (Large Language Models) are neural networks trained on large text datasets to understand and generate language.<br>
VLMs (Vision-Language Models) add a visual encoder so the model can process images and text together.<br> 
A combined VLM+LLM system is often referred to as a multimodal model.

These models can be large—hundreds of millions to billions of parameters—which impacts accuracy, memory use, and runtime speed.<br>
On edge devices like the RK3588, available RAM and compute are limited, and even the NPU has strict constraints on supported operations.<br>
Because of this, models typically need to be quantised or simplified to fit.

Performance is usually expressed in tokens (words) per second.<br>
Once converted to RKNN, parts of the model can run on the NPU, improving speed.<br>
Despite these limits, models like InternVL3 run well on the RK3588 because the NPU efficiently accelerates the heavy math, and the vision encoder can be optimised. This makes advanced multimodal AI feasible on small, power-efficient devices.

------------

## Model performance benchmark (FPS)

All models, with C++ examples, can be found on the Q-engineering GitHub.<br><br>
All LLM models are quantized to **w8a8**, while the VLM vision encoders use **fp16**.<br>

| model         | RAM (GB) | llm cold sec<sup>1</sup> | llm warm sec<sup>2</sup> | vlm cold sec<sup>1</sup> | vlm warm sec<sup>2</sup> | Resolution | Tokens/s |
| --------------| :--: | :-----: | :-----: | :--------: | :-----: | :--------:  | :--------: |
| Qwen2         | | 29.1 |   2.5 | 17.1  | 1.7 | 392 x 392 | 12.5 |
| InternVL3-1B  | 1.3 |  6.8 |   1.1 | 7.8    | 0.75 | 448 x 448 | 30 |
| SmolVLM2-2.2B | | 21.2 |   2.6 | 10.5   | 0.9  | 392 x 392 | 11 |
| SmolVLM2-500M | |  4.8 |   0.7 | 2.5    | 0.25 | 392 x 392 | 31 |
| SmolVLM2-256M | |  1.1 |   0.4 | 2.5    | 0.25 | 392 x 392 | 54 |

<sup>1</sup> When an llm/vlm model is loaded for the first time from your disk to RAM or NPU, it is called a cold start.<br>
The duration depends on your OS, I/O transfer rate, and memory mapping.<br><br> 
<sup>2</sup> Subsequent loading (warm start) takes advantage of the already mapped data in RAM. Mostly, only a few pointers need to be restored.<br>

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
$ git clone https://github.com/Qengineering/InternVL3-NPU.git
```

#### RKLLM, RKNN
To run InternVL3, you need to have the **rkllm-runtime** library version **1.2.2** (or higher) installed, as well as the **rknpu driver** version **0.9.8**.<br>
If you don't have these on your machine, or if you have a lower version, you need to install them.<br>
We have provided the correct versions in the repo.<br>
```bash
$ cd ./InternVL3-NPU/aarch64/library
$ sudo cp ./*.so /usr/local/lib
$ cd ./InternVL3-NPU/aarch64/include
$ sudo cp ./*.h /usr/local/include
```
### Download the LLM and VLM model.
The next step is downloading the models.<br>
This time, we used the original model supplied by Rockchips [rkllm_model_zoo](https://console.box.lenovo.com/l/l0tXb8) (44 GB!), fetch code: rkllm.<br><br>
Better to download only the two needed files (1.5 GB) from our Sync.com server:<br>
[internvl3-1b_w8a8_rk3588.rkllm](https://ln5.sync.com/dl/2ac529d30#49g27fih-qe8mjmu9-prmas7px-uuyua5te) and [internvl3-1b_vision_fp16_rk3588.rknn](https://ln5.sync.com/dl/b565d2360#wmbmdbum-tk36pehc-5t4irskd-7gb6kfti)<br>
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
VLM_NPU ./Moon.jpg ./models/internvl3-1b_vision_fp16_rk3588.rknn ./models/internvl3-1b_w8a8_rk3588.rkllm 2048 4096
```
The NewTokens (2048) and ContextLength (4096) are optional and can be omitted.
### Using the app.
Using the application is simple. Once you provide the image and the models, you can ask everything you want.<br>
Remember, we are on a bare Rock5C, so don't expect the same quality answers as ChatGPT can provide.<br>
On the other hand, when you see the examples below, the app performs amazingly well.<br><br>
If you want to talk about the picture, you need to include the token `<image>` in your prompt once.<br>
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
| ![Singapore_S](https://github.com/user-attachments/assets/e742059a-3bdb-41be-9647-e7767da12ce6) |**User**: <image>Which city is this?<br>**Answer**: This image shows Singapore's iconic skyline at sunset. The distinctive Marina Bay Sands and the ArtScience Museum are prominent landmarks in Singapore. | 
| ![ChineseWall_S](https://github.com/user-attachments/assets/3ba4b57a-4b22-4050-b995-9599c6d239de) |**User**: <image>Tell me something about the history of this place.<br>**Answer**: The Great Wall of China is an ancient defensive structure that has been built over thousands of years to protect against invasions from northern nomadic tribes. The wall stretches across central China, connecting the north and south of the country. It was constructed by the Chinese dynasties during the Qin Dynasty (221-206 BCE) and later expanded under various rulers including the Ming Dynasty (1368-1644). The Great Wall is not only a testament to engineering prowess but also a symbol of China's historical resilience and cultural heritage.|
| ![Pizza_S](https://github.com/user-attachments/assets/26fd5da3-f491-4b48-978a-14eae0819247) |**User**: <image>What are the ingredients I need to make this?<br>**Answer**: To make this pizza, you'll need:<br>1. **Pizza Dough**: This is typically made from flour, water, yeast, and other ingredients.<br> 2. **Tomato Sauce**: A rich tomato-based sauce that provides flavor and color.<br> 3. **Cheese**: Mozzarella or another type of cheese for the melted cheese topping.<br> 4. **Fresh Basil Leaves**: For garnish and added freshness.<br>You can also add toppings like pepperoni, mushrooms, olives, or meats depending on your preference.<br><br>**User**: What is the first step?<br>**Answer**: The first step to making this pizza involves preparing the dough. Here's what you'll need:<br>1. **Prepare the Dough**:<br>**Ingredients**:<br>2 cups of all-purpose flour<br>3 teaspoons of active dry yeast<br>4 tablespoons of warm water (around 105°F to 115°F)<br>1 teaspoon of salt<br>**Instructions**:<br>1. In a large mixing bowl, combine the flour and salt.<br>2. Add the warm water and yeast mixture, stirring until the dough starts to come together.<br>3. Knead the dough on a floured surface for about 5 minutes, until it becomes smooth and elastic.<br>4. Place the dough in a lightly oiled bowl, cover with a damp cloth, and let it rise in a warm place for about 1 hour, or until doubled in size.<br>This will give you a soft, elastic pizza crust that will be great for your delicious homemade pizza!|
| ![Titanic_S](https://github.com/user-attachments/assets/bf6da76c-c9a4-41c6-bea1-2b4411dd4013) |**User**: <image>Which movie is this?<br>**Answer**: This image appears to be from the movie "The Little Mermaid." It shows two characters on a ship at sunset, with one reaching out towards the sea. The setting and costumes suggest it's from the Disney film series.|

## LLM example.
Notice the nice Markdown makeup of the answer!<br><br>
**User**: Can you make me a C++ call that embeds the Pythagorean theorem?<br><br>
**Answer**: Certainly! The Pythagorean theorem is a fundamental principle in geometry stating that in a right-angled triangle, the square of the length of the hypotenuse (the side opposite the right angle) is equal to the sum of the squares of the lengths of the other two sides. In mathematical terms:

\[ c^2 = a^2 + b^2 \]

where \( c \) is the length of the hypotenuse, and \( a \) and \( b \) are the lengths of the other two sides.

To implement this in C++, you can use the following code snippet. This example will calculate the distance between two points using the Pythagorean theorem:

```cpp
#include <iostream>
#include <cmath> // For sqrt function

// Function to calculate the Euclidean distance between two points (x1, y1) and (x2, y2)
double calculateDistance(double x1, double y1, double x2, double y2) {
    return std::sqrt((x2 - x1) * (x2 - x1) + (y2 - y1) * (y2 - y1));
}

int main() {
    // Coordinates of the two points
    double x1 = 3.0;
    double y1 = 4.0;
    double x2 = 6.0;
    double y2 = 8.0;

    // Calculate the distance using the Pythagorean theorem
    double distance = calculateDistance(x1, y1, x2, y2);

    // Output the result
    std::cout << "The distance between points (" << x1 << ", " << y1 << ") and (" << x2 << ", " << y2 << ") is: " << distance << std::endl;

    return 0;
}
```

### Explanation:
1. **Include Necessary Headers**: The `#include <iostream>` header is used for input-output operations, and the `#include <cmath>` header provides access to mathematical functions like `sqrt`.

2. **Function Definition**:
   - `double calculateDistance(double x1, double y1, double x2, double y2)`: This function takes four parameters representing the coordinates of two points in a 2D plane and returns the Euclidean distance between them.

3. **Main Function**:
   - The main function initializes the coordinates of the two points.
   - It then calls `calculateDistance` to compute the distance using the Pythagorean theorem.
   - Finally, it prints out the calculated distance.

This code will output the distance between the points (3, 4) and (6, 8), which is approximately 5.196152.

Feel free to modify the coordinates or use this as a starting point for other geometric calculations!

------------

[![paypal](https://qengineering.eu/images/TipJarSmall4.png)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=CPZTM5BB3FCYL) 




