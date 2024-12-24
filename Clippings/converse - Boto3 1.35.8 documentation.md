---
title: "converse - Boto3 1.35.8 documentation"
source: "https://boto3.amazonaws.com/v1/documentation/api/1.35.8/reference/services/bedrock-runtime/client/converse.html"
author:
published:
created: 2024-12-13
description:
tags:
  - "clippings"
---
[BedrockRuntime](https://boto3.amazonaws.com/v1/documentation/api/1.35.8/reference/services/bedrock-runtime.html) / Client / converse

BedrockRuntime.Client.converse(*\*\*kwargs*)[#](https://boto3.amazonaws.com/v1/documentation/api/1.35.8/reference/services/bedrock-runtime/client/#BedrockRuntime.Client.converse "Permalink to this definition")

Sends messages to the specified Amazon Bedrock model. `Converse` provides a consistent interface that works with all models that support messages. This allows you to write code once and use it with different models. If a model has unique inference parameters, you can also pass those unique parameters to the model.

Amazon Bedrock doesn’t store any text, images, or documents that you provide as content. The data is only used to generate the response.

For information about the Converse API, see *Use the Converse API* in the *Amazon Bedrock User Guide*. To use a guardrail, see *Use a guardrail with the Converse API* in the *Amazon Bedrock User Guide*. To use a tool with a model, see *Tool use (Function calling)* in the *Amazon Bedrock User Guide*

For example code, see *Converse API examples* in the *Amazon Bedrock User Guide*.

This operation requires permission for the `bedrock:InvokeModel` action.

See also: [AWS API Documentation](https://docs.aws.amazon.com/goto/WebAPI/bedrock-runtime-2023-09-30/Converse)

### Request Syntax

```
response = client.converse(
    modelId='string',
    messages=[
        {
            'role': 'user'|'assistant',
            'content': [
                {
                    'text': 'string',
                    'image': {
                        'format': 'png'|'jpeg'|'gif'|'webp',
                        'source': {
                            'bytes': b'bytes'
                        }
                    },
                    'document': {
                        'format': 'pdf'|'csv'|'doc'|'docx'|'xls'|'xlsx'|'html'|'txt'|'md',
                        'name': 'string',
                        'source': {
                            'bytes': b'bytes'
                        }
                    },
                    'toolUse': {
                        'toolUseId': 'string',
                        'name': 'string',
                        'input': {...}|[...]|123|123.4|'string'|True|None
                    },
                    'toolResult': {
                        'toolUseId': 'string',
                        'content': [
                            {
                                'json': {...}|[...]|123|123.4|'string'|True|None,
                                'text': 'string',
                                'image': {
                                    'format': 'png'|'jpeg'|'gif'|'webp',
                                    'source': {
                                        'bytes': b'bytes'
                                    }
                                },
                                'document': {
                                    'format': 'pdf'|'csv'|'doc'|'docx'|'xls'|'xlsx'|'html'|'txt'|'md',
                                    'name': 'string',
                                    'source': {
                                        'bytes': b'bytes'
                                    }
                                }
                            },
                        ],
                        'status': 'success'|'error'
                    },
                    'guardContent': {
                        'text': {
                            'text': 'string',
                            'qualifiers': [
                                'grounding_source'|'query'|'guard_content',
                            ]
                        }
                    }
                },
            ]
        },
    ],
    system=[
        {
            'text': 'string',
            'guardContent': {
                'text': {
                    'text': 'string',
                    'qualifiers': [
                        'grounding_source'|'query'|'guard_content',
                    ]
                }
            }
        },
    ],
    inferenceConfig={
        'maxTokens': 123,
        'temperature': ...,
        'topP': ...,
        'stopSequences': [
            'string',
        ]
    },
    toolConfig={
        'tools': [
            {
                'toolSpec': {
                    'name': 'string',
                    'description': 'string',
                    'inputSchema': {
                        'json': {...}|[...]|123|123.4|'string'|True|None
                    }
                }
            },
        ],
        'toolChoice': {
            'auto': {}
            ,
            'any': {}
            ,
            'tool': {
                'name': 'string'
            }
        }
    },
    guardrailConfig={
        'guardrailIdentifier': 'string',
        'guardrailVersion': 'string',
        'trace': 'enabled'|'disabled'
    },
    additionalModelRequestFields={...}|[...]|123|123.4|'string'|True|None,
    additionalModelResponseFieldPaths=[
        'string',
    ]
)
```

Parameters:

- **modelId** (*string*) –

**\[REQUIRED\]**

The identifier for the model that you want to call.

The `modelId` to provide depends on the type of model that you use:

- If you use a base model, specify the model ID or its ARN. For a list of model IDs for base models, see [Amazon Bedrock base model IDs (on-demand throughput)](https://docs.aws.amazon.com/bedrock/latest/userguide/model-ids.html#model-ids-arns) in the Amazon Bedrock User Guide.
- If you use a provisioned model, specify the ARN of the Provisioned Throughput. For more information, see [Run inference using a Provisioned Throughput](https://docs.aws.amazon.com/bedrock/latest/userguide/prov-thru-use.html) in the Amazon Bedrock User Guide.
- If you use a custom model, first purchase Provisioned Throughput for it. Then specify the ARN of the resulting provisioned model. For more information, see [Use a custom model in Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/model-customization-use.html) in the Amazon Bedrock User Guide.
- **messages** (*list*) –

**\[REQUIRED\]**

The messages that you want to send to the model.

- *(dict) –*

A message input, or returned from, a call to [Converse](https://docs.aws.amazon.com/bedrock/latest/APIReference/API_runtime_Converse.html) or [ConverseStream](https://docs.aws.amazon.com/bedrock/latest/APIReference/API_runtime_ConverseStream.html).

- **role** *(string) –* **\[REQUIRED\]**

The role that the message plays in the message.
- **content** *(list) –* **\[REQUIRED\]**

The message content. Note the following restrictions:

- You can include up to 20 images. Each image’s size, height, and width must be no more than 3.75 MB, 8000 px, and 8000 px, respectively.
- You can include up to five documents. Each document’s size must be no more than 4.5 MB.
- If you include a `ContentBlock` with a `document` field in the array, you must also include a `ContentBlock` with a `text` field.
- You can only include images and documents if the `role` is `user`.

- *(dict) –*

A block of content for a message that you pass to, or receive from, a model with the [Converse](https://docs.aws.amazon.com/bedrock/latest/APIReference/API_runtime_Converse.html) or [ConverseStream](https://docs.aws.amazon.com/bedrock/latest/APIReference/API_runtime_ConverseStream.html) API operations.

### Note

This is a Tagged Union structure. Only one of the following top level keys can be set: `text`, `image`, `document`, `toolUse`, `toolResult`, `guardContent`.

- **text** *(string) –*

Text to include in the message.
- **image** *(dict) –*

Image to include in the message.

### Note

This field is only supported by Anthropic Claude 3 models.

- **format** *(string) –* **\[REQUIRED\]**

The format of the image.
- **source** *(dict) –* **\[REQUIRED\]**

The source for the image.

### Note

This is a Tagged Union structure. Only one of the following top level keys can be set: `bytes`.

- **bytes** *(bytes) –*

The raw image bytes for the image. If you use an AWS SDK, you don’t need to encode the image bytes in base64.
- **document** *(dict) –*

A document to include in the message.

- **format** *(string) –* **\[REQUIRED\]**

The format of a document, or its extension.
- **name** *(string) –* **\[REQUIRED\]**

A name for the document. The name can only contain the following characters:

- Alphanumeric characters
- Whitespace characters (no more than one in a row)
- Hyphens
- Parentheses
- Square brackets

### Note

This field is vulnerable to prompt injections, because the model might inadvertently interpret it as instructions. Therefore, we recommend that you specify a neutral name.
- **source** *(dict) –* **\[REQUIRED\]**

Contains the content of the document.

### Note

This is a Tagged Union structure. Only one of the following top level keys can be set: `bytes`.

- **bytes** *(bytes) –*

The raw bytes for the document. If you use an Amazon Web Services SDK, you don’t need to encode the bytes in base64.
- **toolUse** *(dict) –*

Information about a tool use request from a model.

- **toolUseId** *(string) –* **\[REQUIRED\]**

The ID for the tool request.
- **name** *(string) –* **\[REQUIRED\]**

The name of the tool that the model wants to use.
- **input** (document) – **\[REQUIRED\]**

The input to pass to the tool.
- **toolResult** *(dict) –*

The result for a tool request that a model makes.

- **toolUseId** *(string) –* **\[REQUIRED\]**

The ID of the tool request that this is the result for.
- **content** *(list) –* **\[REQUIRED\]**

The content for tool result content block.

- *(dict) –*

The tool result content block.

### Note

This is a Tagged Union structure. Only one of the following top level keys can be set: `json`, `text`, `image`, `document`.

- **json** (document) –

A tool result that is JSON format data.
- **text** *(string) –*

A tool result that is text.
- **image** *(dict) –*

A tool result that is an image.

### Note

This field is only supported by Anthropic Claude 3 models.

- **format** *(string) –* **\[REQUIRED\]**

The format of the image.
- **source** *(dict) –* **\[REQUIRED\]**

The source for the image.

### Note

This is a Tagged Union structure. Only one of the following top level keys can be set: `bytes`.

- **bytes** *(bytes) –*

The raw image bytes for the image. If you use an AWS SDK, you don’t need to encode the image bytes in base64.
- **document** *(dict) –*

A tool result that is a document.

- **format** *(string) –* **\[REQUIRED\]**

The format of a document, or its extension.
- **name** *(string) –* **\[REQUIRED\]**

A name for the document. The name can only contain the following characters:

- Alphanumeric characters
- Whitespace characters (no more than one in a row)
- Hyphens
- Parentheses
- Square brackets

### Note

This field is vulnerable to prompt injections, because the model might inadvertently interpret it as instructions. Therefore, we recommend that you specify a neutral name.
- **source** *(dict) –* **\[REQUIRED\]**

Contains the content of the document.

### Note

This is a Tagged Union structure. Only one of the following top level keys can be set: `bytes`.

- **bytes** *(bytes) –*

The raw bytes for the document. If you use an Amazon Web Services SDK, you don’t need to encode the bytes in base64.
- **status** *(string) –*

The status for the tool result content block.

### Note

This field is only supported Anthropic Claude 3 models.
- **guardContent** *(dict) –*

Contains the content to assess with the guardrail. If you don’t specify `guardContent` in a call to the Converse API, the guardrail (if passed in the Converse API) assesses the entire message.

For more information, see *Use a guardrail with the Converse API* in the *Amazon Bedrock User Guide*. `</p>`

### Note

This is a Tagged Union structure. Only one of the following top level keys can be set: `text`.

- **text** *(dict) –*

The text to guard.

- **text** *(string) –* **\[REQUIRED\]**

The text that you want to guard.
- **qualifiers** *(list) –*

The qualifier details for the guardrails contextual grounding filter.

- *(string) –*
- **system** (*list*) –

A system prompt to pass to the model.

- *(dict) –*

A system content block.

### Note

This is a Tagged Union structure. Only one of the following top level keys can be set: `text`, `guardContent`.

- **text** *(string) –*

A system prompt for the model.
- **guardContent** *(dict) –*

A content block to assess with the guardrail. Use with the [Converse](https://docs.aws.amazon.com/bedrock/latest/APIReference/API_runtime_Converse.html) or [ConverseStream](https://docs.aws.amazon.com/bedrock/latest/APIReference/API_runtime_ConverseStream.html) API operations.

For more information, see *Use a guardrail with the Converse API* in the *Amazon Bedrock User Guide*.

### Note

This is a Tagged Union structure. Only one of the following top level keys can be set: `text`.

- **text** *(dict) –*

The text to guard.

- **text** *(string) –* **\[REQUIRED\]**

The text that you want to guard.
- **qualifiers** *(list) –*

The qualifier details for the guardrails contextual grounding filter.

- *(string) –*
- **inferenceConfig** (*dict*) –

Inference parameters to pass to the model. `Converse` supports a base set of inference parameters. If you need to pass additional parameters that the model supports, use the `additionalModelRequestFields` request field.

- **maxTokens** *(integer) –*

The maximum number of tokens to allow in the generated response. The default value is the maximum allowed value for the model that you are using. For more information, see [Inference parameters for foundation models](https://docs.aws.amazon.com/bedrock/latest/userguide/model-parameters.html).
- **temperature** *(float) –*

The likelihood of the model selecting higher-probability options while generating a response. A lower value makes the model more likely to choose higher-probability options, while a higher value makes the model more likely to choose lower-probability options.

The default value is the default value for the model that you are using. For more information, see [Inference parameters for foundation models](https://docs.aws.amazon.com/bedrock/latest/userguide/model-parameters.html).
- **topP** *(float) –*

The percentage of most-likely candidates that the model considers for the next token. For example, if you choose a value of 0.8 for `topP`, the model selects from the top 80% of the probability distribution of tokens that could be next in the sequence.

The default value is the default value for the model that you are using. For more information, see [Inference parameters for foundation models](https://docs.aws.amazon.com/bedrock/latest/userguide/model-parameters.html).
- **stopSequences** *(list) –*

A list of stop sequences. A stop sequence is a sequence of characters that causes the model to stop generating the response.

- *(string) –*
- **toolConfig** (*dict*) –

Configuration information for the tools that the model can use when generating a response.

### Note

This field is only supported by Anthropic Claude 3, Cohere Command R, Cohere Command R+, and Mistral Large models.

- **tools** *(list) –* **\[REQUIRED\]**

An array of tools that you want to pass to a model.

- *(dict) –*

Information about a tool that you can use with the Converse API. For more information, see [Tool use (function calling)](https://docs.aws.amazon.com/bedrock/latest/userguide/tool-use.html) in the Amazon Bedrock User Guide.

### Note

This is a Tagged Union structure. Only one of the following top level keys can be set: `toolSpec`.

- **toolSpec** *(dict) –*

The specfication for the tool.

- **name** *(string) –* **\[REQUIRED\]**

The name for the tool.
- **description** *(string) –*

The description for the tool.
- **inputSchema** *(dict) –* **\[REQUIRED\]**

The input schema for the tool in JSON format.

### Note

This is a Tagged Union structure. Only one of the following top level keys can be set: `json`.

- **json** (document) –

The JSON schema for the tool. For more information, see [JSON Schema Reference](https://json-schema.org/understanding-json-schema/reference).
- **toolChoice** *(dict) –*

If supported by model, forces the model to request a tool.

### Note

This is a Tagged Union structure. Only one of the following top level keys can be set: `auto`, `any`, `tool`.

- **auto** *(dict) –*

(Default). The Model automatically decides if a tool should be called or whether to generate text instead.
- **any** *(dict) –*

The model must request at least one tool (no text is generated).
- **tool** *(dict) –*

The Model must request the specified tool. Only supported by Anthropic Claude 3 models.

- **name** *(string) –* **\[REQUIRED\]**

The name of the tool that the model must request.
- **guardrailConfig** (*dict*) –

Configuration information for a guardrail that you want to use in the request.

- **guardrailIdentifier** *(string) –* **\[REQUIRED\]**

The identifier for the guardrail.
- **guardrailVersion** *(string) –* **\[REQUIRED\]**

The version of the guardrail.
- **trace** *(string) –*

The trace behavior for the guardrail.
- **additionalModelRequestFields** (document) – Additional inference parameters that the model supports, beyond the base set of inference parameters that `Converse` supports in the `inferenceConfig` field. For more information, see [Model parameters](https://docs.aws.amazon.com/bedrock/latest/userguide/model-parameters.html).
- **additionalModelResponseFieldPaths** (*list*) –

Additional model parameters field paths to return in the response. `Converse` returns the requested fields as a JSON Pointer object in the `additionalModelResponseFields` field. The following is example JSON for `additionalModelResponseFieldPaths`.

`[ "/stop_sequence" ]`

For information about the JSON Pointer syntax, see the [Internet Engineering Task Force (IETF)](https://datatracker.ietf.org/doc/html/rfc6901) documentation.

`Converse` rejects an empty JSON Pointer or incorrectly structured JSON Pointer with a `400` error code. if the JSON Pointer is valid, but the requested field is not in the model response, it is ignored by `Converse`.

- *(string) –*

Return type:

dict

Returns:

### Response Syntax

```
{
    'output': {
        'message': {
            'role': 'user'|'assistant',
            'content': [
                {
                    'text': 'string',
                    'image': {
                        'format': 'png'|'jpeg'|'gif'|'webp',
                        'source': {
                            'bytes': b'bytes'
                        }
                    },
                    'document': {
                        'format': 'pdf'|'csv'|'doc'|'docx'|'xls'|'xlsx'|'html'|'txt'|'md',
                        'name': 'string',
                        'source': {
                            'bytes': b'bytes'
                        }
                    },
                    'toolUse': {
                        'toolUseId': 'string',
                        'name': 'string',
                        'input': {...}|[...]|123|123.4|'string'|True|None
                    },
                    'toolResult': {
                        'toolUseId': 'string',
                        'content': [
                            {
                                'json': {...}|[...]|123|123.4|'string'|True|None,
                                'text': 'string',
                                'image': {
                                    'format': 'png'|'jpeg'|'gif'|'webp',
                                    'source': {
                                        'bytes': b'bytes'
                                    }
                                },
                                'document': {
                                    'format': 'pdf'|'csv'|'doc'|'docx'|'xls'|'xlsx'|'html'|'txt'|'md',
                                    'name': 'string',
                                    'source': {
                                        'bytes': b'bytes'
                                    }
                                }
                            },
                        ],
                        'status': 'success'|'error'
                    },
                    'guardContent': {
                        'text': {
                            'text': 'string',
                            'qualifiers': [
                                'grounding_source'|'query'|'guard_content',
                            ]
                        }
                    }
                },
            ]
        }
    },
    'stopReason': 'end_turn'|'tool_use'|'max_tokens'|'stop_sequence'|'guardrail_intervened'|'content_filtered',
    'usage': {
        'inputTokens': 123,
        'outputTokens': 123,
        'totalTokens': 123
    },
    'metrics': {
        'latencyMs': 123
    },
    'additionalModelResponseFields': {...}|[...]|123|123.4|'string'|True|None,
    'trace': {
        'guardrail': {
            'modelOutput': [
                'string',
            ],
            'inputAssessment': {
                'string': {
                    'topicPolicy': {
                        'topics': [
                            {
                                'name': 'string',
                                'type': 'DENY',
                                'action': 'BLOCKED'
                            },
                        ]
                    },
                    'contentPolicy': {
                        'filters': [
                            {
                                'type': 'INSULTS'|'HATE'|'SEXUAL'|'VIOLENCE'|'MISCONDUCT'|'PROMPT_ATTACK',
                                'confidence': 'NONE'|'LOW'|'MEDIUM'|'HIGH',
                                'action': 'BLOCKED'
                            },
                        ]
                    },
                    'wordPolicy': {
                        'customWords': [
                            {
                                'match': 'string',
                                'action': 'BLOCKED'
                            },
                        ],
                        'managedWordLists': [
                            {
                                'match': 'string',
                                'type': 'PROFANITY',
                                'action': 'BLOCKED'
                            },
                        ]
                    },
                    'sensitiveInformationPolicy': {
                        'piiEntities': [
                            {
                                'match': 'string',
                                'type': 'ADDRESS'|'AGE'|'AWS_ACCESS_KEY'|'AWS_SECRET_KEY'|'CA_HEALTH_NUMBER'|'CA_SOCIAL_INSURANCE_NUMBER'|'CREDIT_DEBIT_CARD_CVV'|'CREDIT_DEBIT_CARD_EXPIRY'|'CREDIT_DEBIT_CARD_NUMBER'|'DRIVER_ID'|'EMAIL'|'INTERNATIONAL_BANK_ACCOUNT_NUMBER'|'IP_ADDRESS'|'LICENSE_PLATE'|'MAC_ADDRESS'|'NAME'|'PASSWORD'|'PHONE'|'PIN'|'SWIFT_CODE'|'UK_NATIONAL_HEALTH_SERVICE_NUMBER'|'UK_NATIONAL_INSURANCE_NUMBER'|'UK_UNIQUE_TAXPAYER_REFERENCE_NUMBER'|'URL'|'USERNAME'|'US_BANK_ACCOUNT_NUMBER'|'US_BANK_ROUTING_NUMBER'|'US_INDIVIDUAL_TAX_IDENTIFICATION_NUMBER'|'US_PASSPORT_NUMBER'|'US_SOCIAL_SECURITY_NUMBER'|'VEHICLE_IDENTIFICATION_NUMBER',
                                'action': 'ANONYMIZED'|'BLOCKED'
                            },
                        ],
                        'regexes': [
                            {
                                'name': 'string',
                                'match': 'string',
                                'regex': 'string',
                                'action': 'ANONYMIZED'|'BLOCKED'
                            },
                        ]
                    },
                    'contextualGroundingPolicy': {
                        'filters': [
                            {
                                'type': 'GROUNDING'|'RELEVANCE',
                                'threshold': 123.0,
                                'score': 123.0,
                                'action': 'BLOCKED'|'NONE'
                            },
                        ]
                    }
                }
            },
            'outputAssessments': {
                'string': [
                    {
                        'topicPolicy': {
                            'topics': [
                                {
                                    'name': 'string',
                                    'type': 'DENY',
                                    'action': 'BLOCKED'
                                },
                            ]
                        },
                        'contentPolicy': {
                            'filters': [
                                {
                                    'type': 'INSULTS'|'HATE'|'SEXUAL'|'VIOLENCE'|'MISCONDUCT'|'PROMPT_ATTACK',
                                    'confidence': 'NONE'|'LOW'|'MEDIUM'|'HIGH',
                                    'action': 'BLOCKED'
                                },
                            ]
                        },
                        'wordPolicy': {
                            'customWords': [
                                {
                                    'match': 'string',
                                    'action': 'BLOCKED'
                                },
                            ],
                            'managedWordLists': [
                                {
                                    'match': 'string',
                                    'type': 'PROFANITY',
                                    'action': 'BLOCKED'
                                },
                            ]
                        },
                        'sensitiveInformationPolicy': {
                            'piiEntities': [
                                {
                                    'match': 'string',
                                    'type': 'ADDRESS'|'AGE'|'AWS_ACCESS_KEY'|'AWS_SECRET_KEY'|'CA_HEALTH_NUMBER'|'CA_SOCIAL_INSURANCE_NUMBER'|'CREDIT_DEBIT_CARD_CVV'|'CREDIT_DEBIT_CARD_EXPIRY'|'CREDIT_DEBIT_CARD_NUMBER'|'DRIVER_ID'|'EMAIL'|'INTERNATIONAL_BANK_ACCOUNT_NUMBER'|'IP_ADDRESS'|'LICENSE_PLATE'|'MAC_ADDRESS'|'NAME'|'PASSWORD'|'PHONE'|'PIN'|'SWIFT_CODE'|'UK_NATIONAL_HEALTH_SERVICE_NUMBER'|'UK_NATIONAL_INSURANCE_NUMBER'|'UK_UNIQUE_TAXPAYER_REFERENCE_NUMBER'|'URL'|'USERNAME'|'US_BANK_ACCOUNT_NUMBER'|'US_BANK_ROUTING_NUMBER'|'US_INDIVIDUAL_TAX_IDENTIFICATION_NUMBER'|'US_PASSPORT_NUMBER'|'US_SOCIAL_SECURITY_NUMBER'|'VEHICLE_IDENTIFICATION_NUMBER',
                                    'action': 'ANONYMIZED'|'BLOCKED'
                                },
                            ],
                            'regexes': [
                                {
                                    'name': 'string',
                                    'match': 'string',
                                    'regex': 'string',
                                    'action': 'ANONYMIZED'|'BLOCKED'
                                },
                            ]
                        },
                        'contextualGroundingPolicy': {
                            'filters': [
                                {
                                    'type': 'GROUNDING'|'RELEVANCE',
                                    'threshold': 123.0,
                                    'score': 123.0,
                                    'action': 'BLOCKED'|'NONE'
                                },
                            ]
                        }
                    },
                ]
            }
        }
    }
}
```

### Response Structure

- *(dict) –*

- **output** *(dict) –*

The result from the call to `Converse`.

### Note

This is a Tagged Union structure. Only one of the following top level keys will be set: `message`. If a client receives an unknown member it will set `SDK_UNKNOWN_MEMBER` as the top level key, which maps to the name or tag of the unknown member. The structure of `SDK_UNKNOWN_MEMBER` is as follows:

```
'SDK_UNKNOWN_MEMBER': {'name': 'UnknownMemberName'}
```

- **message** *(dict) –*

The message that the model generates.

- **role** *(string) –*

The role that the message plays in the message.
- **content** *(list) –*

The message content. Note the following restrictions:

- You can include up to 20 images. Each image’s size, height, and width must be no more than 3.75 MB, 8000 px, and 8000 px, respectively.
- You can include up to five documents. Each document’s size must be no more than 4.5 MB.
- If you include a `ContentBlock` with a `document` field in the array, you must also include a `ContentBlock` with a `text` field.
- You can only include images and documents if the `role` is `user`.

- *(dict) –*

A block of content for a message that you pass to, or receive from, a model with the [Converse](https://docs.aws.amazon.com/bedrock/latest/APIReference/API_runtime_Converse.html) or [ConverseStream](https://docs.aws.amazon.com/bedrock/latest/APIReference/API_runtime_ConverseStream.html) API operations.

### Note

This is a Tagged Union structure. Only one of the following top level keys will be set: `text`, `image`, `document`, `toolUse`, `toolResult`, `guardContent`. If a client receives an unknown member it will set `SDK_UNKNOWN_MEMBER` as the top level key, which maps to the name or tag of the unknown member. The structure of `SDK_UNKNOWN_MEMBER` is as follows:

```
'SDK_UNKNOWN_MEMBER': {'name': 'UnknownMemberName'}
```

- **text** *(string) –*

Text to include in the message.
- **image** *(dict) –*

Image to include in the message.

### Note

This field is only supported by Anthropic Claude 3 models.

- **format** *(string) –*

The format of the image.
- **source** *(dict) –*

The source for the image.

### Note

This is a Tagged Union structure. Only one of the following top level keys will be set: `bytes`. If a client receives an unknown member it will set `SDK_UNKNOWN_MEMBER` as the top level key, which maps to the name or tag of the unknown member. The structure of `SDK_UNKNOWN_MEMBER` is as follows:

```
'SDK_UNKNOWN_MEMBER': {'name': 'UnknownMemberName'}
```

- **bytes** *(bytes) –*

The raw image bytes for the image. If you use an AWS SDK, you don’t need to encode the image bytes in base64.
- **document** *(dict) –*

A document to include in the message.

- **format** *(string) –*

The format of a document, or its extension.
- **name** *(string) –*

A name for the document. The name can only contain the following characters:

- Alphanumeric characters
- Whitespace characters (no more than one in a row)
- Hyphens
- Parentheses
- Square brackets

### Note

This field is vulnerable to prompt injections, because the model might inadvertently interpret it as instructions. Therefore, we recommend that you specify a neutral name.
- **source** *(dict) –*

Contains the content of the document.

### Note

This is a Tagged Union structure. Only one of the following top level keys will be set: `bytes`. If a client receives an unknown member it will set `SDK_UNKNOWN_MEMBER` as the top level key, which maps to the name or tag of the unknown member. The structure of `SDK_UNKNOWN_MEMBER` is as follows:

```
'SDK_UNKNOWN_MEMBER': {'name': 'UnknownMemberName'}
```

- **bytes** *(bytes) –*

The raw bytes for the document. If you use an Amazon Web Services SDK, you don’t need to encode the bytes in base64.
- **toolUse** *(dict) –*

Information about a tool use request from a model.

- **toolUseId** *(string) –*

The ID for the tool request.
- **name** *(string) –*

The name of the tool that the model wants to use.
- **input** (document) –

The input to pass to the tool.
- **toolResult** *(dict) –*

The result for a tool request that a model makes.

- **toolUseId** *(string) –*

The ID of the tool request that this is the result for.
- **content** *(list) –*

The content for tool result content block.

- *(dict) –*

The tool result content block.

### Note

This is a Tagged Union structure. Only one of the following top level keys will be set: `json`, `text`, `image`, `document`. If a client receives an unknown member it will set `SDK_UNKNOWN_MEMBER` as the top level key, which maps to the name or tag of the unknown member. The structure of `SDK_UNKNOWN_MEMBER` is as follows:

```
'SDK_UNKNOWN_MEMBER': {'name': 'UnknownMemberName'}
```

- **json** (document) –

A tool result that is JSON format data.
- **text** *(string) –*

A tool result that is text.
- **image** *(dict) –*

A tool result that is an image.

### Note

This field is only supported by Anthropic Claude 3 models.

- **format** *(string) –*

The format of the image.
- **source** *(dict) –*

The source for the image.

### Note

This is a Tagged Union structure. Only one of the following top level keys will be set: `bytes`. If a client receives an unknown member it will set `SDK_UNKNOWN_MEMBER` as the top level key, which maps to the name or tag of the unknown member. The structure of `SDK_UNKNOWN_MEMBER` is as follows:

```
'SDK_UNKNOWN_MEMBER': {'name': 'UnknownMemberName'}
```

- **bytes** *(bytes) –*

The raw image bytes for the image. If you use an AWS SDK, you don’t need to encode the image bytes in base64.
- **document** *(dict) –*

A tool result that is a document.

- **format** *(string) –*

The format of a document, or its extension.
- **name** *(string) –*

A name for the document. The name can only contain the following characters:

- Alphanumeric characters
- Whitespace characters (no more than one in a row)
- Hyphens
- Parentheses
- Square brackets

### Note

This field is vulnerable to prompt injections, because the model might inadvertently interpret it as instructions. Therefore, we recommend that you specify a neutral name.
- **source** *(dict) –*

Contains the content of the document.

### Note

This is a Tagged Union structure. Only one of the following top level keys will be set: `bytes`. If a client receives an unknown member it will set `SDK_UNKNOWN_MEMBER` as the top level key, which maps to the name or tag of the unknown member. The structure of `SDK_UNKNOWN_MEMBER` is as follows:

```
'SDK_UNKNOWN_MEMBER': {'name': 'UnknownMemberName'}
```

- **bytes** *(bytes) –*

The raw bytes for the document. If you use an Amazon Web Services SDK, you don’t need to encode the bytes in base64.
- **status** *(string) –*

The status for the tool result content block.

### Note

This field is only supported Anthropic Claude 3 models.
- **guardContent** *(dict) –*

Contains the content to assess with the guardrail. If you don’t specify `guardContent` in a call to the Converse API, the guardrail (if passed in the Converse API) assesses the entire message.

For more information, see *Use a guardrail with the Converse API* in the *Amazon Bedrock User Guide*. `</p>`

### Note

This is a Tagged Union structure. Only one of the following top level keys will be set: `text`. If a client receives an unknown member it will set `SDK_UNKNOWN_MEMBER` as the top level key, which maps to the name or tag of the unknown member. The structure of `SDK_UNKNOWN_MEMBER` is as follows:

```
'SDK_UNKNOWN_MEMBER': {'name': 'UnknownMemberName'}
```

- **text** *(dict) –*

The text to guard.

- **text** *(string) –*

The text that you want to guard.
- **qualifiers** *(list) –*

The qualifier details for the guardrails contextual grounding filter.

- *(string) –*
- **stopReason** *(string) –*

The reason why the model stopped generating output.
- **usage** *(dict) –*

The total number of tokens used in the call to `Converse`. The total includes the tokens input to the model and the tokens generated by the model.

- **inputTokens** *(integer) –*

The number of tokens sent in the request to the model.
- **outputTokens** *(integer) –*

The number of tokens that the model generated for the request.
- **totalTokens** *(integer) –*

The total of input tokens and tokens generated by the model.
- **metrics** *(dict) –*

Metrics for the call to `Converse`.

- **latencyMs** *(integer) –*

The latency of the call to `Converse`, in milliseconds.
- **additionalModelResponseFields** (document) –

Additional fields in the response that are unique to the model.
- **trace** *(dict) –*

A trace object that contains information about the Guardrail behavior.

- **guardrail** *(dict) –*

The guardrail trace object.

- **modelOutput** *(list) –*

The output from the model.

- *(string) –*
- **inputAssessment** *(dict) –*

The input assessment.

- *(string) –*

- *(dict) –*

A behavior assessment of the guardrail policies used in a call to the Converse API.

- **topicPolicy** *(dict) –*

The topic policy.

- **topics** *(list) –*

The topics in the assessment.

- *(dict) –*

Information about a topic guardrail.

- **name** *(string) –*

The name for the guardrail.
- **type** *(string) –*

The type behavior that the guardrail should perform when the model detects the topic.
- **action** *(string) –*

The action the guardrail should take when it intervenes on a topic.
- **contentPolicy** *(dict) –*

The content policy.

- **filters** *(list) –*

The content policy filters.

- *(dict) –*

The content filter for a guardrail.

- **type** *(string) –*

The guardrail type.
- **confidence** *(string) –*

The guardrail confidence.
- **action** *(string) –*

The guardrail action.
- **wordPolicy** *(dict) –*

The word policy.

- **customWords** *(list) –*

Custom words in the assessment.

- *(dict) –*

A custom word configured in a guardrail.

- **match** *(string) –*

The match for the custom word.
- **action** *(string) –*

The action for the custom word.
- **managedWordLists** *(list) –*

Managed word lists in the assessment.

- *(dict) –*

A managed word configured in a guardrail.

- **match** *(string) –*

The match for the managed word.
- **type** *(string) –*

The type for the managed word.
- **action** *(string) –*

The action for the managed word.
- **sensitiveInformationPolicy** *(dict) –*

The sensitive information policy.

- **piiEntities** *(list) –*

The PII entities in the assessment.

- *(dict) –*

A Personally Identifiable Information (PII) entity configured in a guardrail.

- **match** *(string) –*

The PII entity filter match.
- **type** *(string) –*

The PII entity filter type.
- **action** *(string) –*

The PII entity filter action.
- **regexes** *(list) –*

The regex queries in the assessment.

- *(dict) –*

A Regex filter configured in a guardrail.

- **name** *(string) –*

The regex filter name.
- **match** *(string) –*

The regesx filter match.
- **regex** *(string) –*

The regex query.
- **action** *(string) –*

The region filter action.
- **contextualGroundingPolicy** *(dict) –*

The contextual grounding policy used for the guardrail assessment.

- **filters** *(list) –*

The filter details for the guardrails contextual grounding filter.

- *(dict) –*

The details for the guardrails contextual grounding filter.

- **type** *(string) –*

The contextual grounding filter type.
- **threshold** *(float) –*

The threshold used by contextual grounding filter to determine whether the content is grounded or not.
- **score** *(float) –*

The score generated by contextual grounding filter.
- **action** *(string) –*

The action performed by the guardrails contextual grounding filter.
- **outputAssessments** *(dict) –*

the output assessments.

- *(string) –*

- *(list) –*

- *(dict) –*

A behavior assessment of the guardrail policies used in a call to the Converse API.

- **topicPolicy** *(dict) –*

The topic policy.

- **topics** *(list) –*

The topics in the assessment.

- *(dict) –*

Information about a topic guardrail.

- **name** *(string) –*

The name for the guardrail.
- **type** *(string) –*

The type behavior that the guardrail should perform when the model detects the topic.
- **action** *(string) –*

The action the guardrail should take when it intervenes on a topic.
- **contentPolicy** *(dict) –*

The content policy.

- **filters** *(list) –*

The content policy filters.

- *(dict) –*

The content filter for a guardrail.

- **type** *(string) –*

The guardrail type.
- **confidence** *(string) –*

The guardrail confidence.
- **action** *(string) –*

The guardrail action.
- **wordPolicy** *(dict) –*

The word policy.

- **customWords** *(list) –*

Custom words in the assessment.

- *(dict) –*

A custom word configured in a guardrail.

- **match** *(string) –*

The match for the custom word.
- **action** *(string) –*

The action for the custom word.
- **managedWordLists** *(list) –*

Managed word lists in the assessment.

- *(dict) –*

A managed word configured in a guardrail.

- **match** *(string) –*

The match for the managed word.
- **type** *(string) –*

The type for the managed word.
- **action** *(string) –*

The action for the managed word.
- **sensitiveInformationPolicy** *(dict) –*

The sensitive information policy.

- **piiEntities** *(list) –*

The PII entities in the assessment.

- *(dict) –*

A Personally Identifiable Information (PII) entity configured in a guardrail.

- **match** *(string) –*

The PII entity filter match.
- **type** *(string) –*

The PII entity filter type.
- **action** *(string) –*

The PII entity filter action.
- **regexes** *(list) –*

The regex queries in the assessment.

- *(dict) –*

A Regex filter configured in a guardrail.

- **name** *(string) –*

The regex filter name.
- **match** *(string) –*

The regesx filter match.
- **regex** *(string) –*

The regex query.
- **action** *(string) –*

The region filter action.
- **contextualGroundingPolicy** *(dict) –*

The contextual grounding policy used for the guardrail assessment.

- **filters** *(list) –*

The filter details for the guardrails contextual grounding filter.

- *(dict) –*

The details for the guardrails contextual grounding filter.

- **type** *(string) –*

The contextual grounding filter type.
- **threshold** *(float) –*

The threshold used by contextual grounding filter to determine whether the content is grounded or not.
- **score** *(float) –*

The score generated by contextual grounding filter.
- **action** *(string) –*

The action performed by the guardrails contextual grounding filter.

### Exceptions

- `BedrockRuntime.Client.exceptions.AccessDeniedException`
- `BedrockRuntime.Client.exceptions.ResourceNotFoundException`
- `BedrockRuntime.Client.exceptions.ThrottlingException`
- `BedrockRuntime.Client.exceptions.ModelTimeoutException`
- `BedrockRuntime.Client.exceptions.InternalServerException`
- `BedrockRuntime.Client.exceptions.ServiceUnavailableException`
- `BedrockRuntime.Client.exceptions.ValidationException`
- `BedrockRuntime.Client.exceptions.ModelNotReadyException`
- `BedrockRuntime.Client.exceptions.ModelErrorException`