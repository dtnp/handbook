# Deprecation Guidelines

The decision to deprecate and disable APIs is a hard one. There's no "official right way" to do so, but there certainly are wrong ways. If deprecating APIs is not done correctly, it can damage a company's brand, reputation, and trust. There are quite a few reasons for doing API deprecation. Reasons to deprecate an API, or a specific version could include:

- The API is insecure
- The API has too many bugs
- The API does not support important use cases
- The API is inefficient

# Moving Fast
Embracing [Agile and Lean](/handbook/agile) methodologies means that deprecating an API's version is more or less a given. New and updated improvements are constantly added to APIs to keep them relevant. This form of deprivation does not have a big impact on the consumer's applications, if a good and stable alternative with the right resources is quickly provided. Then there's the more permanent form of deprecation — deleting or disabling an API entirely. Here the API is completely disposed of, with no updated version provided. This is a more risky option, and organizations need to have a solid understanding of the implications of doing so, if they want to go through it. Some of the reasons APIs get disposed of are:

- The service can no longer be supported due to costs
- The company's decision to invest resources in a different variant of the product
- The service no longer serves a business objective

These decisions usually boil down to business strategy and the alignment of the APIs with the company's revenue or user acquisition objectives. While this decision is hard, a lot of organizations will have to go through with it at some point.

# Deprecation Empathy

Deprecation is the final stage for an API, and should be done so with care and empathy. It can cause a lot of frustration for your internal and external consumers, and in some cases, can lead to the shutdown of an entire product. Here are a few guidelines to do this better.

# Communication

Be open and honest with the API's consumers when you decide to deprecate an API. Send an initial message announcing the intended deprecation, with the proposed deprecation time frame, next version release schedule (if any), and additional information like support, contact information, etc. 

## Default Sunset Period

The default sunset period for an API or a specific version is **6 months** after announcement

## Non-Standard Sunset Period

There will be times when end points will be sunset at much quicker timelines.  When this happens, sufficient notice will be given to the API consumers with a well articulated explanation for not using the standard default of **6 months**

## Deprecation Lists

To see what is currently scheduled for deprecation, please see the [Active Deprecation List](/handbook/deprecations/active_list.md)
To see what is historical list of deprecated endpoints, please see the [Historical Deprecation List](/handbook/deprecations/historical_list.md)