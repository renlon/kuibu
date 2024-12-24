---
title: "New RAG evaluation and LLM-as-a-judge capabilities in Amazon Bedrock | Amazon Web Services"
source: "https://aws.amazon.com/blogs/aws/new-rag-evaluation-and-llm-as-a-judge-capabilities-in-amazon-bedrock/"
author:
  - "[[Amazon Web Services]]"
published: 2024-12-01
created: 2024-12-12
description: "Evaluate AI models and applications efficiently with Amazon Bedrock’s new LLM-as-a-judge capability for model evaluation and RAG evaluation for Knowledge Bases, offering a variety of quality and responsible AI metrics at scale."
tags:
  - "clippings"
---


Today, we’re announcing two new evaluation capabilities in [Amazon Bedrock](https://aws.amazon.com/bedrock/) that can help you streamline testing and improve [generative AI](https://aws.amazon.com/ai/generative-ai/) applications:

**Amazon Bedrock Knowledge Bases now supports RAG evaluation (preview)** – You can now run an automatic knowledge base evaluation to assess and optimize [Retrieval Augmented Generation (RAG)](https://aws.amazon.com/what-is/retrieval-augmented-generation/) applications using [Amazon Bedrock Knowledge Bases](https://aws.amazon.com/bedrock/knowledge-bases/). The evaluation process uses a [large language model (LLM)](https://aws.amazon.com/what-is/large-language-model/) to compute the metrics for the evaluation. With RAG evaluations, you can compare different configurations and tune your settings to get the results you need for your use case.

**Amazon Bedrock Model Evaluation now includes LLM-as-a-judge (preview)** – You can now perform tests and evaluate other models with humanlike quality at a fraction of the cost and time of running human evaluations.

These new capabilities make it easier to go into production by providing fast, automated evaluation of AI-powered applications, shortening feedback loops and speeding up improvements. These evaluations assess multiple quality dimensions including correctness, helpfulness, and responsible AI criteria such as answer refusal and harmfulness.

To make it easy and intuitive, the evaluation results provide natural language explanations for each score in the output and on console, and the scores are normalized from 0 to 1 for ease of interpretability. Rubrics are published in full with the judge prompts in the documentation so non-scientists can understand how scores are derived.

Let’s see how they work in practice.

**Using RAG evaluations in Amazon Bedrock Knowledge Bases  
**In the Amazon Bedrock console, I choose **Evaluations** in the **Inference and Assessment** section. There, I see the new **Knowledge Bases** tab.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/kb-eval-section.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/kb-eval-section.png)

I choose **Create**, enter a name and a description for the evaluation, and select the **Evaluator model** that will compute the metrics. In this case, I use Anthropic’s **Claude 3.5 Sonnet**.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/kb-eval-create.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/kb-eval-create.png)

I select the knowledge base to evaluate. I previously created a knowledge base containing only the [AWS Lambda Developer Guide PDF file](https://docs.aws.amazon.com/lambda/). In this way, for the evaluation, I can ask questions about the [AWS Lambda](https://aws.amazon.com/lambda/) service.

I can evaluate either the retrieval function alone or the complete retrieve-and-generate workflow. This choice affects the metrics that are available in the next step. I choose to evaluate both retrieval and response generation and select the model to use. In this case, I use Anthropic’s **Claude 3 Haiku**. I can also use [Amazon Bedrock Guardrails](https://aws.amazon.com/bedrock/guardrails/) and adjust runtime inference settings by choosing the **configurations** link after the response generator model.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/kb-eval-create-details-haiku.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/kb-eval-create-details-haiku.png)

Now, I can choose which metrics to evaluate. I select **Helpfulness** and **Correctness** in the **Quality** section and **Harmfulness** in the **Responsible AI metrics** section.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/maaj-eval-create-metrics.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/maaj-eval-create-metrics.png)

Now, I select the dataset that will be used for evaluation. This is the [JSONL](https://jsonlines.org/) file I prepared and uploaded to [Amazon Simple Storage Service (Amazon S3)](https://aws.amazon.com/s3/) for this evaluation. Each line provides a conversation, and for each message there is a reference response.

I specify the S3 location in which to store the results of the evaluation. The evaluation job requires that the S3 bucket is [configured with the cross-origin resource sharing (CORS) permissions described in the Amazon Bedrock User Guide](https://docs.aws.amazon.com/bedrock/latest/userguide/model-evaluation-security-iam.html#model-evaluation-security-cors).

For service access, I need to create or provide an [AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam/) service role that Amazon Bedrock can assume and that allows access to the Amazon Bedrock and Amazon S3 resources used by the evaluation.

After a few minutes, the evaluation has completed, and I browse the results. The actual duration of an evaluation depends on the size of the prompt dataset and on the generator and the evaluator models used.

At the top, the **Metric summary** evaluates the overall performance using the average score across all conversations.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/kb-eval-result-summary.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/kb-eval-result-summary.png)

After that, the **Generation metrics breakdown** gives me details about each of the selected evaluation metrics. My evaluation dataset was small (two lines), so there isn’t a large distribution to look at.

[![](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/kb-eval-result-metrics.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/kb-eval-result-metrics.png)

From here, I can also see example conversations and how they were rated. To view all conversations, I can visit the full output in the S3 bucket.

I’m curious why **Helpfulness** is slightly below one. I expand and zoom **Example conversations** for **Helpfulness**. There, I see the generated output, the ground truth that I provided with the evaluation dataset, and the score. I choose the score to see the model reasoning. According to the model, it would have helped to have more in-depth information. Models really are strict judges.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/21/kb-eval-result-example-conversations.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/21/kb-eval-result-example-conversations.png)

**Comparing RAG evaluations  
**The result of a knowledge base evaluation can be difficult to interpret by itself. For this reason, the console allows comparing results from multiple evaluations to understand the differences. In this way, you can understand if you’re improving or not for the metrics you care about.

For example, I previously ran two other knowledge base evaluations. They’re related to knowledge bases with the same data sources but different chunking and parsing configurations and different embedding models.

I select the two evaluations and choose **Compare**. To be comparable in the console, the evaluations need to cover the same metrics.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/22/kb-eval-compare.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/22/kb-eval-compare.png)

In the **At a glance** tab, I see a visual comparison of the metrics using a spider chart. In this case, the results are not much different. The main difference is the **Faithfulness** score.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/22/kb-eval-compare-visual.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/22/kb-eval-compare-visual.png)

In the **Evaluation details** tab, I find a detailed comparison of the results for each metric, including the difference in scores.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/22/kb-eval-compare-details.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/22/kb-eval-compare-details.png)

**Using LLM-as-a-judge in Amazon Bedrock Model Evaluation (preview)  
**In the Amazon Bedrock console, I choose **Evaluations** in the **Inference and Assessment** section of the navigation pane. After I choose **Create**, I select the new **Automatic: Model as a judge** option.

I enter a name and a description for the evaluation and select the **Evaluator model** that is used to generate evaluation metrics. I use Anthropic’s **Claude 3.5 Sonnet**.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/maaj-eval-create.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/maaj-eval-create.png)

Then, I select the **Generator model**, which is the model I want to evaluate. Model evaluation can help me understand if a smaller and more cost-effective model meets the needs of my use case. I use Anthropic’s **Claude 3 Haiku**.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/maaj-eval-create-generator-model.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/maaj-eval-create-generator-model.png)

In the next section I select the **Metrics** to evaluate. I select **Helpfulness** and **Correctness** in the **Quality** section and **Harmfulness** in the **Responsible AI metrics** section.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/maaj-eval-create-metrics-1.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/19/maaj-eval-create-metrics-1.png)

In the **Datasets** section I specify the Amazon S3 location where my evaluation dataset is stored and the folder in an S3 bucket where the results of the model evaluation job are stored.

For the evaluation dataset, I prepared another JSONL file. Each line provides a prompt and a reference answer. Note that the format is different compared to knowledge base evaluations.

Finally, I can choose an IAM service role that gives Amazon Bedrock access to the resources used by this evaluation job.

I complete the creation of the evaluation. After a few minutes, the evaluation is complete. Similar to the knowledge base evaluation, the result starts with a **Metrics Summary**.

[![](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/21/maaj-eval-result-summary.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/21/maaj-eval-result-summary.png)

The **Generation metrics breakdown** details each metric, and I can look at details for a few sample prompts. I look at **Helpfulness** to better understand the evaluation score.

[![Console screenshot.](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/21/maaj-eval-result-metrics.png)](https://d2908q01vomqb2.cloudfront.net/da4b9237bacccdf19c0760cab7aec4a8359010b0/2024/11/21/maaj-eval-result-metrics.png)

The prompts in the evaluation have been correctly processed by the model, and I can apply the results for my use case. If my application needs to manage prompts similar to the ones used in this evaluation, the evaluated model is a good choice.

**Things to know  
**These new evaluation capabilities are available in preview in the following [AWS Regions](https://aws.amazon.com/about-aws/global-infrastructure/regions_az/):

- RAG evaluation in US East (N. Virginia), US West (Oregon), Asia Pacific (Mumbai, Sydney, Tokyo), Canada (Central), Europe (Frankfurt, Ireland, London, Paris), and South America (São Paulo)
- LLM-as-a-judge in US East (N. Virginia), US West (Oregon), Asia Pacific (Mumbai, Seoul, Sydney, Tokyo), Canada (Central), Europe (Frankfurt, Ireland, London, Paris, Zurich), and South America (São Paulo)

Note that the available evaluator models depend on the Region.

Pricing is based on the standard [Amazon Bedrock pricing](https://aws.amazon.com/bedrock/pricing/) for model inference. There are no additional charges for evaluation jobs themselves. The evaluator models and models being evaluated are billed according to their normal on-demand or provisioned pricing. The judge prompt templates are part of the input tokens, and those judge prompts can be found in the AWS documentation for transparency.

The evaluation service is optimized for English language content at launch, though the underlying models can work with content in other languages they support.

To get started, visit the [Amazon Bedrock console](https://console.aws.amazon.com/bedrock). To learn more, you can access the [Amazon Bedrock documentation](https://docs.aws.amazon.com/bedrock/latest/userguide/what-is-bedrock.html) and send feedback to [AWS re:Post for Amazon Bedrock](https://repost.aws/tags/TAQeKlaPaNRQ2tWB6P7KrMag/amazon-bedrock). You can find deep-dive technical content and discover how our Builder communities are using Amazon Bedrock at [community.aws](https://community.aws/). Let us know what you build with these new capabilities!

— [Danilo](https://twitter.com/danilop)