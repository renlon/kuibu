---
title: "Generating JSON with the Amazon Bedrock Converse API"
source: "https://community.aws/content/2hWA16FSt2bIzKs0Z1fgJBwu589/generating-json-with-the-amazon-bedrock-converse-api?lang=en"
author:
  - "[[Community.aws]]"
published:
created: 2024-12-13
description: "Learn how to return JSON output from large language models using Amazon Bedrock Converse API’s tool use capabilities."
tags:
  - "clippings"
---
## Learn how to return JSON output from large language models using Amazon Bedrock Converse API’s tool use capabilities.

This article is part of a series on tool use with Amazon Bedrock. In part 1, I provided a [quick tutorial on the Amazon Bedrock Converse API](https://community.aws/content/2hHgVE7Lz6Jj1vFv39zSzzlCilG/getting-started-with-the-amazon-bedrock-converse-api). In part 2, I introduced [how to use tools with the Converse API](https://community.aws/content/2hW5367isgQOkkXLYjp4JB3Pe16/intro-to-tool-use-with-the-amazon-bedrock-converse-api). In this article, I’ll demonstrate using Amazon Bedrock large language models to generate JSON that conforms to a pre-defined JSON Schema. I’ll walk through some key JSON Schema elements and a Python code example.

The Amazon Bedrock Converse API provides a consistent way to access large language models (LLMs) using Amazon Bedrock. It supports turn-based messages between the user and the generative AI model. It also provides a consistent format for tool definitions for the models that support tool use (aka “function calling”).

**Tool use** is a technique that allows a large language model to tell the calling application to invoke a function with parameters supplied by the model. The available tools and supported parameters are passed to the model along with a prompt. The model then can select a tool and define parameters according to the tool definition's JSON Schema. The calling application then calls the function and passes the provided parameters to it.

We can also use this tool use capability to skip the function call and just use the generated JSON directly. The example in this article will demonstrate how it works.

Why is JSON generation important? It allows us to more reliably convert unstructured content into structured data. In the past, massive amounts of unstructured data was buried and practically unusable, locked away in documents, customer feedback, and notes fields. For the most part, we only had keyword search as a means to work with that content at scale. With LLM-based JSON generation, we now have the ability to convert that raw content into quantitative data that we can better process and understand.

Generating JSON with the Amazon Bedrock Converse API follows these steps:

1. The calling application passes (A) a tool definition that includes the desired JSON schema and (B) a triggering message to the large language model.
2. The model generates a tool use request, including the JSON conforming to the tool definition’s JSON schema.
3. The calling application extracts the JSON from the model’s tool use request and processes it.

You’ll want to have the latest AWS SDK and Amazon Bedrock model access configured before proceeding:

- Select an AWS region that supports the Anthropic Claude 3 Sonnet model. I'm using us-west-2 (Oregon). You can check the documentation for [model support by region](https://docs.aws.amazon.com/bedrock/latest/userguide/models-regions.html).

- Large language models are non-deterministic. You should expect different results than those shown in this article.
- If you run this code from your own AWS account, you will be charged for the tokens consumed.
- I generally subscribe to a “Minimum Viable Prompt” philosophy. You may need to write more detailed prompts for your use case.
- Not every model supports all of the capabilities of the Converse API, so it’s important to review the [supported model features](https://docs.aws.amazon.com/bedrock/latest/userguide/conversation-inference.html#conversation-inference-supported-models-features) in the official documentation.

Let’s say we’re an investment management company, and we get thousands of customer emails per day to a shared inbox. Normally we need our customer service team to review each email and decide if an action is required, and who should handle it. We might then store that information in a database or pass the data along to another application for further processing. But what if we could use generative AI to interpret the customer request, extract information from it, and recommend which team should handle the request?

**We want to take a request like this:**

> Dear Acme Investments,I am writing to compliment one of your customer service representatives, Shirley Scarry. I recently had the pleasure of speaking with Shirley regarding my account deposit. Shirley was extremely helpful and knowledgeable, and went above and beyond to ensure that all of my questions were answered. Shirley also had Robert Herbford join the call, who franky seemed distracted. My wife Clara had some more detailed questions that Shirley was able to resolve for us.Shirley's professionalism and expertise were greatly appreciated, and I would be happy to recommend Acme Investments to others based on my experience.Sincerely,Carson Bradford

**And extract data from it like this:**

`1   2   3   4   5   6   7   8   9   10   11   12   13   14   15   16   17   18   19   20   21   `

`{       "summary": "Customer compliments Acme Investments employee Shirley Scarry for her excellent customer service in resolving account deposit questions, but notes Robert Herbford seemed distracted on the call.",       "escalate_complaint": false,       "overall_sentiment": "Positive",       "supporting_business_unit": "Customer Service",       "level_of_concern": 2,       "customer_names": [           "Carson Bradford",           "Clara Bradford"       ],       "sentiment_towards_employees": [           {               "employee_name": "Shirley Scarry",               "sentiment": "Positive"           },           {               "employee_name": "Robert Herbford",               "sentiment": "Negative"           }       ]   }`

We’ll do this by:

1. Defining a tool that specifies a JSON Schema for the desired output.
2. Directly using the JSON generated by a Converse API tool use request.

Let’s start by reviewing the various components of a Converse API tool definition. I’ll share the full tool definition in the Python code later in this article.

We’ll start by defining a toolSpec element, and give the tool a name and a description. The name and description will help the model correctly determine when to request the tool.

`1   2   3   4   `

`{       "toolSpec": {           "name": "summarize_email",           "description": "Summarize email content.",`

I highly recommend reading Anthropic’s [Best practices for tool definitions](https://docs.anthropic.com/en/docs/tool-use#best-practices-for-tool-definitions) - although Amazon Bedrock uses a slightly different tool definition format, these best practices are still applicable.

The inputSchema is where we put the JSON schema for the JSON we want generated.

`1   2   3   4   `

`"inputSchema": {       "json": {           "type": "object",           "properties": {`

Now we can define the properties we want returned in the JSON response.

This fragment shows how to define a string property:

`1   2   3   4   `

`"summary": {       "type": "string",       "description": "A brief one-line or two-line summary of the email."   },`

That will become something like this in the generated JSON:

`1   `

`"summary": "Customer compliments Acme Investments employee Shirley Scarry for her excellent customer service in resolving account deposit questions, but notes Robert Herbford seemed distracted on the call.",`

This fragment shows how to define a boolean property:

`1   2   3   4   `

`"escalate_complaint": {       "type": "boolean",       "description": "Indicates if this email is serious enough to be immediately escalated for further review."   },`

That will become something like this in the generated JSON:

`1   `

`"escalate_complaint": false,`

This fragment shows how to define a numeric property. It also specifies a range between 1 and 10, inclusive.

`1   2   3   4   5   6   `

`"level_of_concern": {       "type": "integer",       "description": "Rate the level of concern for the above content on a scale from 1-10",       "minimum": 1,       "maximum": 10   },`

That will become something like this in the generated JSON:

These fragments show how to define a string property with the enum keyword. This limits the allowed values to those listed for the enum.

`1   2   3   4   5   `

`"overall_sentiment": {       "type": "string",       "description": "The sender's overall sentiment.",       "enum": ["Positive", "Neutral", "Negative"]   },`

`1   2   3   4   5   `

`"supporting_business_unit": {       "type": "string",       "description": "The internal business unit that this email should be routed to.",       "enum": ["Sales", "Operations", "Customer Service", "Fund Management"]   },`

That will become something like this in the generated JSON:

`1   2   `

`"overall_sentiment": "Positive",   "supporting_business_unit": "Customer Service",`

This fragment shows how to define a property that contains a list of strings. The property has type `array` and the items have type `string`.

`1   2   3   4   5   `

`"customer_names": {       "type": "array",       "description": "An array of customer names mentioned in the email.",       "items": { "type": "string" }   },`

That will become something like this in the generated JSON:

`1   2   3   4   `

`"customer_names": [       "Carson Bradford",       "Clara Bradford"   ],`

This fragment shows how to define a property that contains a list of objects. The property has type `array` and the items have type `object`. We then define the properties for the objects in the list (`employee_name` and `sentiment`).

`1   2   3   4   5   6   7   8   9   10   11   12   13   14   15   16   17   `

`"sentiment_towards_employees": {       "type": "array",       "items": {           "type": "object",           "properties": {               "employee_name": {                   "type": "string",                   "description": "The employee's name."               },               "sentiment": {                   "type": "string",                   "description": "The sender's sentiment towards the employee.",                   "enum": ["Positive", "Neutral", "Negative"]               }           }       }   }`

That will become something like this in the generated JSON:

`1   2   3   4   5   6   7   8   9   10   `

`"sentiment_towards_employees": [       {           "employee_name": "Shirley Scarry",           "sentiment": "Positive"       },       {           "employee_name": "Robert Herbford",           "sentiment": "Negative"       }   ]`

Finally, we specify that all of the above properties are required. Any properties not included in this list would be considered optional.

`1   2   3   4   5   6   7   8   9   `

`"required": [       "summary",       "escalate_complaint",       "overall_sentiment",       "supporting_business_unit",       "level_of_concern",       "customer_names",       "sentiment_towards_employees"   ]`

We use the `bedrock-runtime` service name to make calls to Amazon Bedrock models.

`1   2   3   4   `

`import boto3, json`

`session = boto3.Session()   bedrock = session.client(service_name='bedrock-runtime')`

Now behold the tool definition in its full glory:

`1   2   3   4   5   6   7   8   9   10   11   12   13   14   15   16   17   18   19   20   21   22   23   24   25   26   27   28   29   30   31   32   33   34   35   36   37   38   39   40   41   42   43   44   45   46   47   48   49   50   51   52   53   54   55   56   57   58   59   60   61   62   63   64   65   66   67   68   69   70   `

`tool_list = [       {           "toolSpec": {               "name": "summarize_email",               "description": "Summarize email content.",               "inputSchema": {                   "json": {                       "type": "object",                       "properties": {                           "summary": {                               "type": "string",                               "description": "A brief one-line or two-line summary of the email."                           },                           "escalate_complaint": {                               "type": "boolean",                               "description": "Indicates if this email is serious enough to be immediately escalated for further review."                           },                           "level_of_concern": {                               "type": "integer",                               "description": "Rate the level of concern for the above content on a scale from 1-10",                               "minimum": 1,                               "maximum": 10                           },                           "overall_sentiment": {                               "type": "string",                               "description": "The sender's overall sentiment.",                               "enum": ["Positive", "Neutral", "Negative"]                           },                           "supporting_business_unit": {                               "type": "string",                               "description": "The internal business unit that this email should be routed to.",                               "enum": ["Sales", "Operations", "Customer Service", "Fund Management"]                           },                           "customer_names": {                               "type": "array",                               "description": "An array of customer names mentioned in the email.",                               "items": { "type": "string" }                           },                           "sentiment_towards_employees": {                               "type": "array",                               "items": {                                   "type": "object",                                   "properties": {                                       "employee_name": {                                           "type": "string",                                           "description": "The employee's name."                                       },                                       "sentiment": {                                           "type": "string",                                           "description": "The sender's sentiment towards the employee.",                                           "enum": ["Positive", "Neutral", "Negative"]                                       }                                   }                               }                           }                       },                       "required": [                           "summary",                           "escalate_complaint",                           "overall_sentiment",                           "supporting_business_unit",                           "level_of_concern",                           "customer_names",                           "sentiment_towards_employees"                       ]                   }               }           }       }   ]   `

Now we define the content to process and merge it into our prompt template:

`1   2   3   4   5   6   7   8   9   10   11   12   13   14   15   16   `

`content = """Dear Acme Investments,`

`I am writing to compliment one of your customer service representatives, Shirley Scarry. I recently had the pleasure of speaking with Shirley regarding my account deposit. Shirley was extremely helpful and knowledgeable, and went above and beyond to ensure that all of my questions were answered. Shirley also had Robert Herbford join the call, who wasn't quite as helpful. My wife, Clara Bradford, didn't like him at all.   Shirley's professionalism and expertise were greatly appreciated, and I would be happy to recommend Acme Investments to others based on my experience.   Sincerely,`

`Carson Bradford   """`

`message = {       "role": "user",       "content": [           { "text": f"<content>{content}</content>" },           { "text": "Please use the summarize_email tool to generate the email summary JSON based on the content within the <content> tags." }       ],   }`

This is one possible approach to merge the email content into your prompt. You could choose not to use XML tags or could use a different prompt for your specific use case.

We’re now ready to pass the tool definition and message to Amazon Bedrock. We specify Anthropic’s Claude 3 Sonnet as the target model. We also configure the toolChoice parameter to force Claude to use the `summarize_email` tool.

`1   2   3   4   5   6   7   8   9   10   11   12   13   14   15   16   `

`response = bedrock.converse(       modelId="anthropic.claude-3-sonnet-20240229-v1:0",       messages=[message],       inferenceConfig={           "maxTokens": 2000,           "temperature": 0       },       toolConfig={           "tools": tool_list,           "toolChoice": {               "tool": {                   "name": "summarize_email"               }           }       }   )   `

As a final step, we now extract and print the JSON from the model’s tool use response:

`1   2   3   4   5   6   7   8   9   10   11   `

`response_message = response['output']['message']`

`response_content_blocks = response_message['content']`

`content_block = next((block for block in response_content_blocks if 'toolUse' in block), None)`

`tool_use_block = content_block['toolUse']`

`tool_result_dict = tool_use_block['input']`

`print(json.dumps(tool_result_dict, indent=4))`

Your script should display something like the following:

`1   2   3   4   5   6   7   8   9   10   11   12   13   14   15   16   17   18   19   20   21   `

`{       "summary": "Customer compliments Acme Investments employee Shirley Scarry for her excellent customer service in resolving account deposit questions, but notes that Robert Herbford seemed distracted during the call.",       "escalate_complaint": false,       "overall_sentiment": "Positive",       "supporting_business_unit": "Customer Service",       "level_of_concern": 2,       "customer_names": [           "Carson Bradford",           "Clara Bradford"       ],       "sentiment_towards_employees": [           {               "employee_name": "Shirley Scarry",               "sentiment": "Positive"           },           {               "employee_name": "Robert Herbford",               "sentiment": "Negative"           }       ]   }`

That looks about right!

I hope this gave you a good sense of how to define a JSON Schema and generate JSON from unstructured content. Now you can try coming up with a schema for your own use case!

- Not every model supports every capability of the Converse API, so it’s important to review the [supported model features](https://docs.aws.amazon.com/bedrock/latest/userguide/conversation-inference.html#conversation-inference-supported-models-features) in the official documentation.

Any opinions in this post are those of the individual author and may not reflect the opinions of AWS.