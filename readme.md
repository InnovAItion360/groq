# Image Analysis with Groq API

This project demonstrates how to use the Groq API to analyze an image using a multimodal language model. The script encodes an image, sends it to the Groq API along with a query, and retrieves the analysis result.

## Prerequisites

- Python 3.7 or higher
- A Groq API key (you can get it from [Groq's website](https://groq.com/))
- An image file to analyze

## Setup

1. **Clone the repository**:
   ```bash
   git clone https://github.com/yourusername/image-analysis-groq.git
   cd image-analysis-groq
   ```

2. **Set up a virtual environment** (optional but recommended):
   ```bash
   python3 -m venv venv
   source venv/bin/activate  # On Windows, use `venv\Scripts\activate`
   ```

3. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

4. **Set up your Groq API key**:
   - Create a `.env` file in the root directory of the project.
   - Add your Groq API key to the `.env` file:
     ```plaintext
     GROQ_API_KEY=your_groq_api_key_here
     ```

## Code

### `devops.py`

```python
# if you don't use pipenv, uncomment the following:
# from dotenv import load_dotenv
# load_dotenv()

# Step 1: Setup GROQ API key
import os

GROQ_API_KEY = os.environ.get("GROQ_API_KEY")

# Step 2: Convert image to required format
import base64

image_path = "01-devops.jpeg"

def encode_image(image_path):
    with open(image_path, "rb") as image_file:
        return base64.b64encode(image_file.read()).decode('utf-8')

# Step 3: Setup Multimodal LLM
from groq import Groq

query = "Is there something wrong with my face?"
model = "llama-3.2-90b-vision-preview"

def analyze_image_with_query(query, model, encoded_image):
    client = Groq(api_key=GROQ_API_KEY)
    messages = [
        {
            "role": "user",
            "content": [
                {
                    "type": "text",
                    "text": query
                },
                {
                    "type": "image_url",
                    "image_url": {
                        "url": f"data:image/jpeg;base64,{encoded_image}",
                    },
                },
            ],
        }
    ]
    chat_completion = client.chat.completions.create(
        messages=messages,
        model=model
    )

    return chat_completion.choices[0].message.content

# Main execution
if __name__ == "__main__":
    encoded_image = encode_image(image_path)
    result = analyze_image_with_query(query, model, encoded_image)
    print("Analysis Result:")
    print(result)
```

### `requirements.txt`

```plaintext
groq
python-dotenv
```

---

## How to Run

1. Place your image file (e.g., `01-devops.jpeg`) in the project directory.
2. Update the `image_path` variable in `main.py` if your image has a different name or path.
3. Run the script:
   ```bash
   python main.py
   ```

4. The script will encode the image, send it to the Groq API, and print the analysis result.

---

## Example Output

If everything is set up correctly, you should see an output similar to the following:

```
Analysis Result:
There is nothing wrong with your face. You look great!
```

---

## Notes

- Replace `your_groq_api_key_here` in the `.env` file with your actual Groq API key.
- Ensure the image file exists in the specified path (`image_path`).
- You can modify the `query` variable to ask different questions about the image.

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
