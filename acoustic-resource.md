---

copyright:
  years: 2017, 2020
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

# Working with audio resources
{: #audioResources}

You can add individual audio files or archive files that contain multiple audio files to a custom acoustic model. The recommended means of adding audio resources is by adding archive files. Creating and adding a single archive file is considerably more efficient than adding multiple audio files individually. You can also submit requests to add multiple different audio resources at the same time.
{: shortdesc}

## Adding an audio resource
{: #addAudioResource}

You use the `POST /v1/acoustic_customizations/{customization_id}/audio/{audio_name}` method to add either type of audio resource to a custom acoustic model. You pass the audio resource as the body of the request and include the following parameters:

-   The `customization_id` path parameter to specify the customization ID of the model.
-   The `audio_name` path parameter to specify a name for the audio resource.
    -   Use a localized name that matches the language of the custom model and reflects the contents of the resource.
    -   Include a maximum of 128 characters in the name.
    -   Do not use characters that need to be URL-encoded. For example, do not use spaces, slashes, backslashes, colons, ampersands, double quotes, plus signs, equals signs, questions marks, and so on in the name. (The service does not prevent the use of these characters. But because they must be URL-encoded wherever used, their use is strongly discouraged.)
    -   Do not use the name of an audio resource that has already been added to the custom model.

When you update a model's audio resources, you must train the model for the changes to take effect during transcription. For more information, see [Train the custom acoustic model](/docs/speech-to-text?topic=speech-to-text-acoustic#trainModel-acoustic).

## Adding an audio file
{: #addAudioType}

To add an individual audio file to a custom acoustic model, you specify the format (MIME type) of the audio with the `Content-Type` header. You can add audio with any format that is supported for use with recognition requests. Include the `rate`, `channels`, and `endianness` parameters with the specification of formats that require them. For more information about the supported audio formats, see [Audio formats](/docs/speech-to-text?topic=speech-to-text-audio-formats).

The `application/octet-stream` specification for an audio format is not supported for audio resources.
{: note}

The following example from [Add audio to the custom acoustic model](/docs/speech-to-text?topic=speech-to-text-acoustic#addAudio) adds an `audio/wav` file:

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/wav" \
--data-binary @audio1.wav \
"{url}/v1/acoustic_customizations/{customization_id}/audio/audio1"
```
{: pre}

## Adding an archive file
{: #addArchiveType}

The preferred means of adding audio to a custom acoustic model is to add an archive file that includes multiple audio files. You can add the following types of archive files by specifying the type of the archive with the `Content-Type` request header:

-   A **.zip** file by specifying `application/zip`
-   A **.tar.gz** file by specifying `application/gzip`

You might also need to specify the `Contained-Content-Type` header depending on the format of the files that you are adding:

-   For audio files of type `audio/alaw`, `audio/basic`, `audio/l16`, or `audio/mulaw`, you must use the `Contained-Content-Type` header to specify the format of the audio files. Include the `rate`, `channels`, and `endianness` parameters where necessary. In this case, all audio files contained in the archive file must have the same audio format.
-   For audio files of all other types, you can omit the `Contained-Content-Type` header. In this case, the audio files contained in the archive file can have any of the formats not listed in the previous bullet. They do not need to have the same format.

Do not use the `Contained-Content-Type` header when adding an audio-type resource.
{: note}

The name of an audio file that is contained in an archive-type resource can include a maximum of 128 characters. This includes the file extension and all elements of the name (for example, slashes).

The following example from [Add audio to the custom acoustic model](/docs/speech-to-text?topic=speech-to-text-acoustic#addAudio) adds an `application/zip` file that contains audio files in `audio/l16` format that are sampled at 16 kHz:

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: application/zip" \
--header "Contained-Content-Type: audio/l16;rate=16000" \
--data-binary @audio2.zip \
"{url}/v1/acoustic_customizations/{customization_id}/audio/audio2"
```
{: pre}

## Guidelines for adding audio resources
{: #audioGuidelines}

The improvement in recognition accuracy that you can expect from using a custom acoustic model depends on a number of factors. These factors include how much audio data the custom acoustic model contains and how similar that data is to the audio that is being transcribed. The improvement also depends on whether the custom acoustic model is trained with a corresponding custom language model.

Follow these guidelines when you add audio resources to a custom acoustic model:

-   Add at least 10 minutes and no more than 200 hours of audio to a custom acoustic model. The audio must include speech, not silence.

    The quality of the audio makes a difference when you are determining how much to add. The better the model's audio reflects the characteristics of the audio that is to be recognized, the better the quality of the custom model for speech recognition. If the audio is of good quality, adding more can improve transcription accuracy. But adding even five to ten hours of good quality audio can make a positive difference.
-   Add audio resources that are no larger than 100 MB. All audio- and archive-type resources are limited to a maximum size of 100 MB.

    To maximize the amount of audio that you can add with a single resource, consider using an audio format that offers compression. For more information, see [Data limits and compression](/docs/speech-to-text?topic=speech-to-text-audio-formats#limits).
-   Divide large audio files into multiple smaller files. Make sure to split the audio between words, at points of silence.

    Because you can submit multiple simultaneous requests to add different audio resources, you can add smaller files concurrently. This parallel approach to adding audio resources can accelerate the service's analysis of your audio.
-   Add audio content that reflects the acoustic channel conditions of the audio that you plan to transcribe. For example, if your application deals with audio that has background noise from a moving vehicle, use the same type of data to build the custom model.
-   Make sure that the sampling rate of an audio file matches the sampling rate of the base model for the custom acoustic model:
    -   For broadband models, the sampling rate must be at least 16 kHz (16,000 samples per second).
    -   For narrowband models, the sampling rate must be at least 8 kHz (8000 samples per second).

    If the sampling rate of the audio is higher than the minimum required sampling rate, the service downsamples the audio to the appropriate rate. If the sampling rate of the audio is lower than the minimum required rate, the service labels the audio file as `invalid`. If any audio file that is contained in an archive file is invalid, the service considers the entire archive invalid.
-   Create a custom language model to use with your custom acoustic model in the following cases:
    -   If your audio is less than an hour long, create a custom language model based on transcriptions of the audio to achieve the best results.
    -   If your audio is domain-specific and contains unique words that are not found in the service's base vocabulary, use language model customization to expand the service's base vocabulary. Acoustic model customization alone cannot produce those words during transcription.

    For more information, see [Using custom acoustic and custom language models together](/docs/speech-to-text?topic=speech-to-text-useBoth).
