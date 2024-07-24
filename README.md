# Using IBM's InstructLab to Run an LLM Locally and Train It with New Data

In this guide, we will learn how to use IBM's InstructLab to run a Large Language Model (LLM) locally and train it with new data. InstructLab is a platform that enables open-source developers to contribute to LLM development and improve the model collectively. It utilizes IBM's novel Large-scale Alignment for chatBots (LAB) instruction-tuning method, which allows assimilating new knowledge and capabilities into the model without overwriting what the model has already learned. 

## Prerequisites
Before we begin, ensure you have the following:
- Access to [HuggingFace](https://huggingface.co/), a leading platform for hosting open-sourced models.
- An AWS EC2 instance with Nvidia GeForce GPUs to accelerate the training process. (Note: AWS EC2 charges a fee, approximately $1 per hour. For more information on setting up an EC2 instance, you can refer to [this article](https://medium.com/@chinmayd49/self-host-llm-with-ec2-vllm-langchain-fastapi-llm-cache-and-huggingface-model-7a2efa2dcdab)).

## Installation
1. Download and install the required tools for InstructLab by following the instructions in the [InstructLab Readme](https://github.com/instructlab/instructlab?tab=readme-ov-file#%EF%B8%8F-initialize-ilab).
2. Watch this [YouTube demo](https://www.youtube.com/watch?v=snMUJGXozec) to gain a better understanding of InstructLab and its usage.

## Running the LLM Locally
1. Once the installation is complete, initialize the InstructLab configuration by running the command: `ilab config init`.
2. Download the pre-trained model using the command: `ilab model download`.
3. Start serving the model locally by running: `ilab model serve`.
4. In a new window, activate your Python virtual environment with the command: `source venv/bin/activate`.
5. To chat with the model served in the other window, run the command: `ilab model chat`.

## Creating New Knowledge and Training the Model
1. Create a Git repository and add two files: `qna.yaml` and `<data>.md` (In this example, we used `Startrek_Homeowners_insurance.md`).
2. Edit the knowledge files:
   - `qna.yaml` should contain the questions and answers you want to train the model with. Include at least six question-answer pairs, and ensure the file references the repository you are using to store the training data and the updated commit. The total word count for Q&A pairs should not exceed 2300 words.
   - `<data>.md` contains the fine-tuning data you wish to train the model with.
3. Push the knowledge files to your repository.

## Training the Model
1. SSH into the EC2 instance and navigate to the `instructlab/` directory.
2. Remove any previous `qna.yaml` file if present and replace it with the latest version using the command:
   ```
   cd instructlab/
   rm taxonomy/knowledge/insurance/qna.yaml
   vi taxonomy/knowledge/insurance/qna.yaml
   ```
   Copy the contents of the new `qna.yaml` file into this editor.
3. Activate the Python virtual environment with the command: `source venv/bin/activate`.
4. Generate synthetic data by running the command: `ilab generate --num-instructions 200`. The time required for this process may vary based on available resources (typically 30 minutes to 1 hour). The generated data will be stored in the `generated` directory, including files named `generated*.json`, `test*.jsonl`, and `train*.jsonl`.
5. Train the model locally with GPU acceleration using the command: `ilab model train --device=cuda`. This command utilizes the newly generated JSON files for training.

## Serving the New Model
1. After the training process completes, serve the new model using the command: `ilab serve --model-path ./models/ggml-model-f16.gguf`.
2. To chat with the newly trained model, run the command: `ilab model chat`.

## Testing and Comparison
1. Test the newly trained model's knowledge by asking questions that were previously answered by the old model.
2. Compare the performance of the old and new models to evaluate the improvements made.

## Tips for AWS EC2 and NVIDIA GPU
- Shut down the EC2 instance when not in use to save costs. You can do this by running the command: `sudo shutdown now`.
- Use an EC2 instance with Nvidia GeForce GPUs for better performance. The instance type used in this guide is `g5.xlarge` running Red Hat Enterprise Linux.
- Install the Nvidia driver to enable GPU acceleration. Use the following commands:
  ```
  sudo dnf module remove nvidia-driver:latest-dkms
  sudo dnf -y module install nvidia-driver:latest-dkms
  ```
  For more information on NVIDIA drivers, you can refer to [this link](https://www.nvidia.com/en-gb/ai-on-rtx/).

## Additional Resources
- [ChatGPT: Generative AI in Insurance](https://aisera.com/blog/chatgpt-generative-ai-in-insurance/)
- [IBM's InstructLab: A New Era for AI Model Creation and Performance](https://www.forbes.com/sites/moorinsights/2024/06/17/ibms-instructlab-a-new-era-for-ai-model-creation-and-performance/)

Now you are ready to run an LLM locally using IBM's InstructLab and train it with new data. Happy experimenting!