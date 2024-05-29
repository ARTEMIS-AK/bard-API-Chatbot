# bard-API-Chatbot

## Chatbot with Bard API

This project demonstrates how to set up and use the Bard API to build a simple chatbot and a custom text summarizer in Python.

## Project Description

### Libraries and Setup
1. **Install Required Libraries**: The necessary libraries for Bard API, HTTP requests, and PDF handling are installed.
    ```python
    !pip install bardapi pypdf
    ```

2. **Import Required Libraries**: The required libraries are imported for interaction with the Bard API and PDF processing.
    ```python
    from bardapi import Bard
    import os
    import time
    from pypdf import PdfReader
    ```

3. **Setup Environment Variables**: The Bard API key is set as an environment variable.
    ```python
    os.environ['_BARD_API_KEY']="g.a000gAgrff7117PPO3pUi2-sw3yuOzdpWmSFOTK0X60llp99EnASfNA91VyRYvIBsxBZLLcB-AACgYKAZ8SAQASFQHGX2Mi24PcNbvaJCYDjGZtbdXJsxoVAUF8yKo9GNJuQn7GYjK1QkjonZS80076"
    ```

### Simple Chatbot
A simple chatbot is created by sending a text query to the Bard API and printing the response.
```python
input_text = "capital of india"
print(Bard().get_answer(input_text)['content'])
```
Output:
```
The capital of India is **New Delhi**. It is part of the National Capital Territory of Delhi (NCT), which also includes the city of Delhi (Old Delhi). New Delhi is home to the seat of all three branches of the Indian government: the Rashtrapati Bhavan (President's House), Sansad Bhavan (Parliament House), and the Supreme Court of India.
```

### Custom Chatbot: Text Summarizer
1. **Mount Google Drive**: If using Google Colab, mount Google Drive to access files.
    ```python
    from google.colab import drive
    drive.mount('/content/drive')
    ```

2. **Reading PDF File**: The PDF file is read using `PdfReader`, and the text from each page is extracted.
    ```python
    directory = '/content/drive/MyDrive/1-Build-a-Chatbot/'
    filename = 'Attention-Is-All-You-Need.pdf'

    pdfFileObject = open(directory + filename, 'rb')
    pdfReader = PdfReader(pdfFileObject)
    text = []

    for i in range(len(pdfReader.pages)):
        page_text = pdfReader.pages[i].extract_text().replace('\t\r', '').replace('\xa0', '')
        text.append(page_text)
    ```

3. **Merging Pages**: To reduce the number of API calls, multiple pages are merged into larger chunks of text.
    ```python
    def join_elements(lst, chars_per_element):
        new_lst = []
        for i in range(0, len(lst), chars_per_element):
            new_lst.append(''.join(lst[i:i + chars_per_element]))
        return new_lst

    new_text = join_elements(text, 3)

    print(f"Original Pages = {len(text)}")
    print(f"Compressed Pages = {len(new_text)}")
    ```

4. **Get Completion Function**: A function to get a summary from the Bard API.
    ```python
    def get_completion(prompt):
        response = Bard().get_answer(prompt)['content']
        return response
    ```

5. **Summarizing Text**: Each chunk of text is sent to the Bard API for summarization, and the responses are concatenated into a final summary.
    ```python
    summary = ''
    
    for i in range(len(new_text)):
        prompt = f"""
        Your task is to act as a Text Summariser.
        I'll give you text from pages of a book from beginning to end.
        And your job is to summarise text from these pages in less than 100 words.
        Don't be conversational. I need a plain 100 word answer.
        Text is shared below, delimited with triple backticks:
        ```{new_text[i]}```
        """
        try:
            response = get_completion(prompt)
        except:
            response = get_completion(prompt)
        
        print(response)
        summary += ' ' + response + '\n\n'
        time.sleep(19)  # Delay to adhere to free usage limits
    ```


## Dependencies
- bardapi
- pypdf
- google.colab (if using Google Colab)
