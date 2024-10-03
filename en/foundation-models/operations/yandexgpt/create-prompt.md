---
title: "How to send a request in prompt mode to {{ yagpt-full-name }}"
description: "Follow this guide to learn how to use {{ yagpt-full-name }} in prompt mode."
---

# Sending a request in prompt mode

To generate text in [prompt mode](../../concepts/index.md#working-mode), send a request to the model using the [completion](../../text-generation/api-ref/TextGeneration/completion.md) method.

## Getting started {#before-begin}

{% include notitle [ai-before-beginning](../../../_includes/foundation-models/yandexgpt/ai-before-beginning.md) %}

## Request to the model via the REST API {#request}

{% list tabs group=programming_language %}

- Bash {#bash}

  {% include [curl](../../../_includes/curl.md) %}
  
  {% include [bash-windows-note-single](../../../_includes/translate/bash-windows-note-single.md) %}

  1. Create a file with the request body, e.g., `body.json`:
  
     ```json
     {
       "modelUri": "gpt://<folder_ID>/yandexgpt-lite",
       "completionOptions": {
         "stream": false,
         "temperature": 0.1,
         "maxTokens": "1000"
       },
       "messages": [
         {
           "role": "system",
           "text": "Translate the text"
         },
         {
           "role": "user",
           "text": "To be, or not to be: that is the question."
         }
       ]
     }
     ```
  
     Where:
  
     {% include [api-parameters](../../../_includes/foundation-models/yandexgpt/api-parameters.md) %}
  
  1. To send the request to the model, run this command:
  
     ```bash
     export FOLDER_ID=<folder_ID>
     export IAM_TOKEN=<IAM_token>
     curl --request POST \
       -H "Content-Type: application/json" \
       -H "Authorization: Bearer ${IAM_TOKEN}" \
       -H "x-folder-id: ${FOLDER_ID}" \
       -d "@<path_to_JSON_file>" \
       "https://llm.{{ api-host }}/foundationModels/v1/completion"
     ```
  
     Where:
  
     * `FOLDER_ID`: ID of the folder for which your account has the `{{ roles-yagpt-user }}` role or higher.
     * `IAM_TOKEN`: IAM token received [before getting started](#before-begin).
  
     {% cut "Result:" %}
  
     ```json
     {
       "result": {
         "alternatives": [
           {
             "message": {
               "role": "assistant",
               "text": "To be or not to be: that is the question."
             },
             "status": "ALTERNATIVE_STATUS_FINAL"
           }
         ],
         "usage": {
           "inputTextTokens": "28",
           "completionTokens": "10",
           "totalTokens": "38"
         },
         "modelVersion": "06.12.2023"
       }
     }
     ```

     {% endcut %}

- Python {#python}

  1. Create a file named `test.py` with the model request code:

     ```python
     import requests
     import argparse
     
     URL = "https://llm.api.cloud.yandex.net/foundationModels/v1/completion"
     
     def run(iam_token, folder_id, user_text):    
         # Building a request
         data = {}
         # Specifying model type
         data["modelUri"] = f"gpt://{folder_id}/yandexgpt"
         # Configuring options
         data["completionOptions"] = {"temperature": 0.3, "maxTokens": 1000}
         # Specifying context for the model
         data["messages"] = [
             {"role": "system", "text": "Correct errors in the text."},
             {"role": "user", "text": f"{user_text}"},
         ]
         
         # Sending the request
         response = requests.post(
             URL,
             headers={
                 "Accept": "application/json",
                 "Authorization": f"Bearer {iam_token}"
             },
             json=data,
         ).json()
     
         #Printing out the result
         print(response)
     
     if __name__ == '__main__':
         parser = argparse.ArgumentParser()
         parser.add_argument("--iam_token", required=True, help="IAM token")
         parser.add_argument("--folder_id", required=True, help="Folder id")
         parser.add_argument("--user_text", required=True, help="User text")
         args = parser.parse_args()
         run(args.iam_token, args.folder_id, args.user_text)
     ```

  1. Run the `test.py` file substituting the [IAM token](../../../iam/concepts/authorization/iam-token.md) and [folder ID](../../../resource-manager/operations/folder/get-id.md) values:

     ```bash
     export IAM_TOKEN=<IAM_token>
     export FOLDER_ID=<folder_ID>
     export TEXT='Erors wont corrct themselfs'
     python test.py \
       --iam_token ${IAM_TOKEN} \
       --folder_id ${FOLDER_ID} \
       --user_text ${TEXT}
     ```

     {% cut "Result:" %}

     ```text
     {'result': {'alternatives': [{'message': {'role': 'assistant', 'text': 'Errors will not correct themselves.'}, 'status': 'ALTERNATIVE_STATUS_FINAL'}], 'usage': {'inputTextTokens': '29', 'completionTokens': '9', 'totalTokens': '38'}, 'modelVersion': '07.03.2024'}}
     ```

     {% endcut %}

{% endlist %}

## Request to the model via the gRPC API {#request-grpc}

{% list tabs group=programming_language %}

- Python {#python}

  {% include [bash-windows-note-single](../../../_includes/translate/bash-windows-note-single.md) %}

  1. Clone the {{ yandex-cloud }} API repository by entering the code into a notebook cell.

     ```bash
     git clone https://github.com/yandex-cloud/cloudapi
     ```

  1. Use the pip package manager to install the `grpcio-tools` package:

     ```bash
     pip install grpcio-tools
     ```

  1. Go to the directory hosting the cloned {{ yandex-cloud }} API repository:

     ```bash
     cd <path_to_cloudapi_folder>
     ```

  1. Create the `output` folder:

     ```bash
     mkdir output
     ```

  1. Generate the client interface code:

     ```bash
     python -m grpc_tools.protoc -I . -I third_party/googleapis \
       --python_out=output \
       --grpc_python_out=output \
         google/api/http.proto \
         google/api/annotations.proto \
         yandex/cloud/api/operation.proto \
         google/rpc/status.proto \
         yandex/cloud/operation/operation.proto \
         yandex/cloud/validation.proto \
         yandex/cloud/ai/foundation_models/v1/text_generation/text_generation_service.proto \
         yandex/cloud/ai/foundation_models/v1/text_common.proto
     ```

  1. In the `output` folder, create a file named `test.py` with the model request code:

     ```python
     # coding=utf8
     import argparse
     import grpc
     
     import yandex.cloud.ai.foundation_models.v1.text_common_pb2 as pb
     import yandex.cloud.ai.foundation_models.v1.text_generation.text_generation_service_pb2_grpc as service_pb_grpc
     import yandex.cloud.ai.foundation_models.v1.text_generation.text_generation_service_pb2 as service_pb
     
     def run(iam_token, folder_id, user_text):
         cred = grpc.ssl_channel_credentials()
         channel = grpc.secure_channel('llm.api.cloud.yandex.net:443', cred)
         stub = service_pb_grpc.TextGenerationServiceStub(channel)
     
         request = service_pb.CompletionRequest(
             model_uri=f"gpt://{folder_id}/yandexgpt",
             completion_options=pb.CompletionOptions(
                 max_tokens={"value": 2000}, 
                 temperature={"value": 0.5}
             ),
         )
         message_system = request.messages.add()
         message_system.role = "system"
         message_system.text = "Correct errors in the text."
     
         message_user = request.messages.add()
         message_user.role = "user"
         message_user.text = user_text
     
         it = stub.Completion(request, metadata=(
             ('authorization', f'Bearer {iam_token}'),
         ))
         for response in it:
             for alternative in response.alternatives:
                 print (alternative.message.text)
     
     if __name__ == '__main__':
         parser = argparse.ArgumentParser()
         parser.add_argument("--iam_token", required=True, help="IAM token")
         parser.add_argument("--folder_id", required=True, help="Folder id")
         parser.add_argument("--user_text", required=True, help="User text")
         args = parser.parse_args()
         run(args.iam_token, args.folder_id, args.user_text)
     ```

  1. Run the `test.py` file substituting the [IAM token](../../../iam/concepts/authorization/iam-token.md) and [folder ID](../../../resource-manager/operations/folder/get-id.md) values:

     ```bash
     export IAM_TOKEN=<IAM_token>
     export FOLDER_ID=<folder_ID>
     export TEXT='Erors wont corrct themselfs'
     python output/test.py \
       --iam_token ${IAM_TOKEN} \
       --folder_id ${FOLDER_ID} \
       --user_text ${TEXT}
     ```

     {% cut "Result:" %}

     ```text
     Errors will not correct themselves.
     ```

     {% endcut %}

{% endlist %}

### Streaming request via the gRPC API {#stream}

With the `stream` parameter enabled, the server will provide not just the final text generation result but intermediate results as well. Each intermediate response contains the whole currently available generation result. Until the final response is received, the generation results may change as new messages arrive. 

The `stream` parameter's operation is most evident when creating and processing large texts.

{% note warning %}

The `stream` parameter is not available for the model's [asynchronous mode](async-request.md).

{% endnote %}

Generate the gRPC client interface code as described in [this guide](#request-grpc). At Step 6, generate a file named `test.py` with the code to access the model.

```python
# coding=utf8
import argparse
import grpc

import yandex.cloud.ai.foundation_models.v1.text_common_pb2 as pb
import yandex.cloud.ai.foundation_models.v1.text_generation.text_generation_service_pb2_grpc as service_pb_grpc
import yandex.cloud.ai.foundation_models.v1.text_generation.text_generation_service_pb2 as service_pb

def run(iam_token, folder_id, user_text):
    cred = grpc.ssl_channel_credentials()
    channel = grpc.secure_channel('llm.api.cloud.yandex.net:443', cred)
    stub = service_pb_grpc.TextGenerationServiceStub(channel)

    request = service_pb.CompletionRequest(
            model_uri=f"gpt://{folder_id}/yandexgpt",
            completion_options=pb.CompletionOptions(
                max_tokens={"value": 2000},
                temperature={"value": 0.5},
                stream=True
            ),
        )
        message_system = request.messages.add()
        message_system.role = "system"
        message_system.text = "Correct errors in the text."
    
        message_user = request.messages.add()
        message_user.role = "user"
        message_user.text = user_text
    
        it = stub.Completion(request, metadata=(
            ('authorization', f'Bearer {iam_token}'),
        ))             
        
        for response in it:
            print(response)

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument("--iam_token", required=True, help="IAM token")
    parser.add_argument("--folder_id", required=True, help="Folder id")
    parser.add_argument("--user_text", required=True, help="User text")
    args = parser.parse_args()
    run(args.iam_token, args.folder_id, args.user_text)
```

{% cut "Result:" %}

```text
alternatives {
  message {
    role: "assistant"
    text: "E"
  }
  status: ALTERNATIVE_STATUS_PARTIAL
}
usage {
  input_text_tokens: 29
  completion_tokens: 1
  total_tokens: 30
}
model_version: "07.03.2024"

alternatives {
  message {
    role: "assistant"
    text: "Errors will not correct themselves."
  }
  status: ALTERNATIVE_STATUS_FINAL
}
usage {
  input_text_tokens: 29
  completion_tokens: 9
  total_tokens: 38
}
model_version: "07.03.2024"
```

{% endcut %}
