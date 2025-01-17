---

copyright:
  years: 2015, 2020
lastupdated: "2020-06-23"

subcollection: speech-to-text

---

{:shortdesc: .shortdesc}
{:external: target="_blank" .external}
{:tip: .tip}
{:important: .important}
{:note: .note}
{:deprecated: .deprecated}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}

# Managing custom language models
{: #manageLanguageModels}

The customization interface includes the `POST /v1/customizations` method for creating a custom language model. The interface also includes the `POST /v1/customizations/train` method for training a custom model on the latest data from its words resource. For more information, see
{: shortdesc}

-   [Create a custom language model](/docs/speech-to-text?topic=speech-to-text-languageCreate#createModel-language)
-   [Train the custom language model](/docs/speech-to-text?topic=speech-to-text-languageCreate#trainModel-language)

In addition, the interface includes methods for listing information about custom language models, resetting a custom model to its initial state, upgrading a custom model, and deleting a custom model. You cannot train, reset, upgrade, or delete a custom model while the service is handling another operation on that model, including adding resources to the model.

## Listing custom language models
{: #listModels-language}

The customization interface provides two methods for listing information about the custom language models that are owned by the specified credentials:

-   The `GET /v1/customizations` method lists information about all custom language models or about all custom language models for a specified language.
-   The `GET /v1/customizations/{customization_id}` method lists information about a specified custom language model. Use this method to poll the service about the status of a training request or a request to add new words.

Both methods return the following information about a custom model:

-   `customization_id` identifies the custom model's Globally Unique Identifier (GUID). The GUID is used to identify the model in methods of the interface.
-   `created` is the date and time in Coordinated Universal Time (UTC) at which the custom model was created.
-   `updated` is the date and time in Coordinated Universal Time (UTC) at which the custom model was last modified.
-   `language` is the language of the custom model.
-   `dialect` is the dialect of the language for the custom model, which does not necessarily match the language of the custom model for Spanish models. For more information, see the description of the `dialect` parameter in [Create a custom language model](/docs/speech-to-text?topic=speech-to-text-languageCreate#createModel-language).
-   `owner` identifies the credentials of the service instance that owns the custom model.
-   `name` is the name of the custom model.
-   `description` shows the description of the custom model, if one was provided at its creation.
-   `base_model` indicates the name of the language model for which the custom model was created.
-   `versions` provides a list of the available versions of the custom model. Each element of the array indicates a version of the base model with which the custom model can be used. Multiple versions exist only if the custom model is upgraded. Otherwise, only a single version is shown. For more information, see [Listing version information for a custom model](/docs/speech-to-text?topic=speech-to-text-customUpgrade#upgradeList).

The method also returns a `status` field that indicates the state of the custom model:

-   `pending` indicates that the model was created. It is waiting either for valid training data (corpora, grammars, or words) to be added or for the service to finish analyzing data that was added.
-   `ready` indicates that the model contains valid data and is ready to be trained. If the model contains a mix of valid and invalid resources, training of the model fails unless you set the `strict` query parameter to `false`. For more information, see [Training failures](/docs/speech-to-text?topic=speech-to-text-languageCreate#failedTraining-language).
-   `training` indicates that the model is being trained on data.
-   `available` indicates that the model is trained and ready to use with a recognition request.
-   `upgrading` indicates that the model is being upgraded.
-   `failed` indicates that training of the model failed. Examine the words in the model's words resource to determine the errors that prevented the model from being trained.

Additionally, the output includes a `progress` field that indicates the current progress of the custom model's training. If you used the `POST /v1/customizations/{customization_id}/train` method to start training the model, this field indicates the current progress of that request as a percentage complete. At this time, the value of the field is `100` if the status is `available`; otherwise, it is `0`.

### Example requests and responses
{: #listExample-language}

The following example includes the `language` query parameter to list all US English custom language models that are owned by the specified credentials:

```bash
curl -X GET -u "apikey:{apikey}" \
"{url}/v1/customizations?language=en-US"
```
{: pre}

The credentials own two such models. The first model is awaiting data or is being processed by the service. The second model is fully trained and ready for use.

```javascript
{
  "customizations": [
    {
      "customization_id": "74f4807e-b5ff-4866-824e-6bba1a84fe96",
      "created": "2016-06-01T18:42:25.324Z",
      "updated": "2016-06-01T18:42:25.324Z",
      "language": "en-US",
      "dialect": "en-US",
      "versions": [
        "en-US_BroadbandModel.v07-06082016.06202016",
        "en-US_BroadbandModel.v2017-11-15"
      ],
      "owner": "297cfd08-330a-22ba-93ce-1a73f454dd98",
      "name": "Example model",
      "description": "Example custom language model",
      "base_model_name": "en-US_BroadbandModel",
      "status": "pending",
      "progress": 0
    },
    {
      "customization_id": "8391f918-3b76-e109-763c-b7732fae4829",
      "created": "2016-06-01T18:51:37.291Z",
      "updated": "2016-06-01T20:02:10.624Z",
      "language": "en-US",
      "dialect": "en-US",
      "versions": [
        "en-US_BroadbandModel.v2017-11-15"
      ],
      "owner": "297cfd08-330a-22ba-93ce-1a73f454dd98",
      "name": "Example model two",
      "description": "Example custom language model two",
      "base_model_name": "en-US_BroadbandModel",
      "status": "available",
      "progress": 100
    }
  ]
}
```
{: codeblock}

The following example returns information about the custom model that has the specified customization ID:

```bash
curl -X GET -u "apikey:{apikey}" \
"{url}/v1/customizations/{customization_id}"
```
{: pre}

```javascript
{
  "customization_id": "74f4807e-b5ff-4866-824e-6bba1a84fe96",
  "created": "2016-06-01T18:42:25.324Z",
  "updated": "2016-06-01T18:42:25.324Z",
  "language": "en-US",
  "dialect": "en-US",
  "versions": [
    "en-US_BroadbandModel.v07-06082016.06202016",
    "en-US_BroadbandModel.v2017-11-15"
  ],
  "owner": "297cfd08-330a-22ba-93ce-1a73f454dd98",
  "name": "Example model",
  "description": "Example custom language model",
  "base_model_name": "en-US_BroadbandModel",
  "status": "pending",
  "progress": 0
}
```
{: codeblock}

## Resetting a custom language model
{: #resetModel-language}

Use the `POST /v1/customizations/{customization_id}/reset` method to reset a custom model. Resetting a model removes all of the corpora and words from the model, initializing the model to its state at creation. The method does not delete the model itself or metadata such as its name and language. However, when you reset a model, its words resource is empty and must be re-created by adding corpora and words.

### Example request
{: #resetExample-language}

The following example resets the custom model with the specified customization ID:

```bash
curl -X POST -u "apikey:{apikey}" \
"{url}/v1/customizations/{customization_id}/reset"
```
{: pre}

## Deleting a custom language model
{: #deleteModel-language}

Use the `DELETE /v1/customizations/{customization_id}` method to delete a custom language model that you no longer need. The method deletes all corpora and words that are associated with the custom model and the model itself. Use this method with caution: a custom model and its data cannot be reclaimed after you delete the model.

### Example request
{: #deleteExample-language}

The following example deletes the custom model with the specified customization ID:

```bash
curl -X DELETE -u "apikey:{apikey}" \
"{url}/v1/customizations/{customization_id}"
```
{: pre}
