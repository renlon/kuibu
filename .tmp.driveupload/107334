---
created: 2025-04-06 14:25
modified_time: Sunday 6th April 2025 14:25:52
draft: false
title: Demystifying AWS Bedrock Converse Tool Use - JSON Schema vs. Function Calling
tags:
  - LLM
  - bedrock
  - aws
  - converseAPI
  - tooluse
---

Have you ever wondered why your AWS Bedrock Converse API calls sometimes return a mysterious `stopReason: "tool_use"`? Or why some developers swear by using tools for JSON validation while others insist on full function calling? 

If you're scratching your head about:
- Why your tool results aren't being processed by the model
- Whether you need to manually execute tools or if they run automatically
- The difference between using tools for JSON validation vs. actual function execution
- How to properly structure your tool results

Then you're in the right place! Let's dive into these questions and more as we explore the two powerful patterns of tool use in AWS Bedrock Converse.

Imagine you're at a restaurant where you can either: 1) ask the chef to create a dish following a specific recipe, or 2) ask the chef to use various kitchen tools to prepare something custom. This is essentially the choice you have when using AWS Bedrock's Converse API for tool use! Let's break down these approaches and reveal the clever tricks that will save you hours of debugging. 

## The Confusion: What Does That `stopReason: "tool_use"` Really Mean?

You've sent a request to the Bedrock Converse API with a tool definition. The [response](https://docs.aws.amazon.com/bedrock/latest/userguide/tool-use-examples.html) comes back with:

```javascript
{
    "output": {
        "message": {
            "role": "assistant",
            "content": [
                {
                    "toolUse": {
                        "toolUseId": "tooluse_kZJMlvQmRJ6eAyJE5GIl7Q",
                        "name": "top_song",
                        "input": {
                            "sign": "WZPZ"
                        }
                    }
                }
            ]
        }
    },
    "stopReason": "tool_use"
}
```

Wait, what's happening here? Has the tool been used? Is it waiting for something? 

**Key insight:** When you see `stopReason: "tool_use"`, the model is telling you, "I need to use this tool to answer your question!" It's like a waiter taking your order but not yet bringing your food. The tool has NOT been executed yet.

## Two Distinct Patterns: Choose Your Adventure

### Pattern 1: The JSON Schema Validator

This is like asking the chef to follow a very specific recipe card - you're not actually using kitchen tools, you're just enforcing a structure! For a detailed guide on this pattern, check out [Generating JSON with the Amazon Bedrock Converse API](https://community.aws/content/2hWA16FSt2bIzKs0Z1fgJBwu589/generating-json-with-the-amazon-bedrock-converse-api?lang=en).

```python
# The clever trick: define a "tool" that's just a JSON schema
tool_config = {
    "tools": [
        {
            "toolSpec": {
                "name": "json_validator",
                "description": "Format output as structured JSON",
                "inputSchema": {"json": your_schema_definition}
            }
        }
    ],
    # Force the model to use this tool
    "toolChoice": {"tool": {"name": "json_validator"}}
}

# Make the API call
response = bedrock_client.converse(
    modelId="anthropic.claude-3-sonnet-20240229-v1:0",
    messages=[{"role": "user", "content": [{"text": prompt}]}],
    toolConfig=tool_config
)

# Extract the structured JSON directly from the toolUse input
structured_data = response["output"]["message"]["content"][0]["toolUse"]["input"]
```

💡 **Pro tip:** Pattern 1 is perfect when you just need well-structured data from the model. You don't actually call any function - you just grab the structured JSON and go on your way!

### Pattern 2: The Full Function Calling Flow (The Complete Conversation)

This is like having a conversation with a chef who needs to check the recipe book (tool) to prepare your dish, then bringing back the cooking instructions for them to complete the meal. According to [AWS documentation](https://docs.aws.amazon.com/bedrock/latest/userguide/tool-use-examples.html), this pattern is particularly useful when you need the model to interact with external services or APIs to gather information before providing a complete response.

```python
# Step 1: Define the actual tool
tool_config = {
    "tools": [
        {
            "toolSpec": {
                "name": "weather_service",
                "description": "Gets current weather for a location",
                "inputSchema": {"json": {
                    "type": "object",
                    "properties": {
                        "location": {"type": "string"},
                        "units": {"type": "string", "enum": ["metric", "imperial"]}
                    },
                    "required": ["location"]
                }}
            }
        }
    ]
}

# Step 2: Initial request
initial_response = bedrock_client.converse(
    modelId="anthropic.claude-3-sonnet-20240229-v1:0",
    messages=[{"role": "user", "content": [{"text": "What's the weather like in Seattle?"}]}],
    toolConfig=tool_config
)

# The model has requested the tool! Let's process it
if initial_response["stopReason"] == "tool_use":
    tool_use = initial_response["output"]["message"]["content"][0]["toolUse"]
    
    # Step 3: Actually call your function
    weather_data = my_weather_api(tool_use["input"]["location"])
    
    # Step 4: Send the result back to complete the conversation
    tool_result = {
        "toolUseId": tool_use["toolUseId"],
        "content": [{"json": weather_data}],
        "status": "success"
    }
    
    # Include both the original conversation and the new tool result
    final_response = bedrock_client.converse(
        modelId="anthropic.claude-3-sonnet-20240229-v1:0",
        messages=[
            {"role": "user", "content": [{"text": "What's the weather like in Seattle?"}]},
            {"role": "assistant", "content": [{"toolUse": tool_use}]},
            {"role": "user", "content": [{"toolResult": tool_result}]}
        ],
        toolConfig=tool_config
    )
    
    # Now we get the model's final response using the weather data
    final_answer = final_response["output"]["message"]["content"][0]["text"]
```

⚠️ **Watch out!** Missing Step 4 is a common mistake. If you don't send the tool results back to the model, your conversation is left hanging!

## 🧪 When to Use Each Pattern

### Use Pattern 1 (JSON Schema) when:
- You just need structured data output from the model
- You don't need the model to reflect on the results
- You want a simple, one-step process
- You're trying to enforce output formatting

### Use Pattern 2 (Full Function Calling) when:
- You need to execute actual functions or APIs
- The model needs to use the function results to complete its response
- You're building a multi-step reasoning process
- You want a complete conversational experience

## 🌟 Real-World Implementation

Let's see a practical example of Pattern 1 to get structured analysis of a troubleshooting scenario:

```python
def analyze_incident(incident_description):
    # Define our JSON schema for incident analysis
    schema = {
        "type": "object",
        "properties": {
            "symptom_summary": {"type": "string"},
            "root_cause_summary": {"type": "string"},
            "mitigation_actions": {"type": "string"},
            "troubleshooting_approaches": {"type": "string"},
            "observation_artifacts": {"type": "string"},
            "root_cause_details": {"type": "string"}
        },
        "required": ["symptom_summary", "root_cause_summary", "mitigation_actions"]
    }
    
    # Configure the tool
    tool_config = {
        "tools": [{
            "toolSpec": {
                "name": "incident_analyzer",
                "description": "Analyzes incident reports",
                "inputSchema": {"json": schema}
            }
        }],
        "toolChoice": {"tool": {"name": "incident_analyzer"}}
    }
    
    # Make the request
    response = bedrock_client.converse(
        modelId="anthropic.claude-3-sonnet-20240229-v1:0",
        messages=[{"role": "user", "content": [{"text": incident_description}]}],
        toolConfig=tool_config
    )
    
    # Extract the structured analysis
    if response["stopReason"] == "tool_use":
        analysis = response["output"]["message"]["content"][0]["toolUse"]["input"]
        return analysis
    else:
        return {"error": "Model didn't use the expected tool"}
```

## Key Takeaways

1. `stopReason: "tool_use"` means the model wants to use a tool but hasn't done so yet
2. Pattern 1 (JSON Schema) is a clever shortcut when you just need structured output
3. Pattern 2 (Full Function Calling) is necessary when you need to execute actual functions
4. Always complete the conversation cycle if using Pattern 2
5. Choose the right pattern based on whether you need the model to process tool results

---

*"The right pattern in the right place is worth a thousand clever hacks."*

# References
- https://docs.aws.amazon.com/bedrock/latest/userguide/tool-use-examples.html
- https://community.aws/content/2hWA16FSt2bIzKs0Z1fgJBwu589/generating-json-with-the-amazon-bedrock-converse-api?lang=en
- https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/bedrock-runtime/client/converse.html
