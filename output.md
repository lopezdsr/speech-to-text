---

copyright:
  years: 2015, 2021
lastupdated: "2021-03-02"

subcollection: speech-to-text

---

{:shortdesc: .shortdesc}
{:external: target="_blank" .external}
{:tip: .tip}
{:important: .important}
{:note: .note}
{:beta: .beta}
{:deprecated: .deprecated}
{:pre: .pre}
{:codeblock: .codeblock}
{:screen: .screen}
{:javascript: .ph data-hd-programlang='javascript'}
{:java: .ph data-hd-programlang='java'}
{:python: .ph data-hd-programlang='python'}
{:swift: .ph data-hd-programlang='swift'}

# Output features
{: #output}

The {{site.data.keyword.speechtotextfull}} service offers the following features to indicate the information that the service is to include in its transcription results for a speech recognition request. All output parameters are optional.
{: shortdesc}

-   For examples of simple speech recognition requests for each of the service's interfaces, see [Making a recognition request](/docs/speech-to-text?topic=speech-to-text-basic-request).
-   For examples and descriptions of speech recognition responses, see [Understanding recognition results](/docs/speech-to-text?topic=speech-to-text-basic-response). The service returns all JSON response content in the UTF-8 character set.
-   For an alphabetized list of all available speech recognition parameters, including their status (generally available or beta) and supported languages, see the [Parameter summary](/docs/speech-to-text?topic=speech-to-text-summary).

## Speaker labels
{: #speaker_labels}

The speaker labels feature is beta functionality that is available for US English, Australian English, German, Japanese, Korean, and Spanish (both broadband and narrowband models) and UK English (narrowband model only).
{: beta}

Speaker labels identify which individuals spoke which words in a multi-participant exchange. (Labeling who spoke and when is sometimes referred to as *speaker diarization*.) You can use the feature to create a person-by-person transcript of an audio stream. For example, you can use it to develop analytics for a call-center or meeting transcript, or to animate an exchange with a conversational robot or avatar. For best performance, use audio that is at least a minute long.

Speaker labels are optimized for two-speaker scenarios. They work best for telephone conversations that involve two people in an extended exchange. They can handle up to six speakers, but more than two speakers can result in variable performance. Two-person exchanges are typically conducted over narrowband media, but you can use speaker labels with supported narrowband and broadband models.

To use the feature, you set the `speaker_labels` parameter to `true` for a recognition request; the parameter is `false` by default. The service identifies speakers by individual words of the audio. It relies on a word's start and end time to identify its speaker. Therefore, enabling speaker labels also forces the `timestamps` parameter to be `true`. For more information, see [Word timestamps](/docs/speech-to-text?topic=speech-to-text-output#word_timestamps).

### Speaker labels example
{: #speakerLabelsExample}

The following example request shows a response that includes speaker labels. The numeric values that are associated with each element of the `timestamps` array are the start and end times of the word in the audio.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/flac" \
--data-binary @{path}audio-multi.flac \
"{url}/v1/recognize?model=en-US_NarrowbandModel&speaker_labels=true"
```
{: pre}

```javascript
{
  "results": [
    {
      "alternatives": [
        {
          "timestamps": [
            [
              "hello",
              0.68,
              1.19
            ],
            [
              "yeah",
              1.47,
              1.93
            ],
            [
              "yeah",
              1.96,
              2.12
            ],
            [
              "how's",
              2.12,
              2.59
            ],
            [
              "Billy",
              2.59,
              3.17
            ],
            . . .
          ]
          "confidence": 0.82,
          "transcript": "hello yeah yeah how's Billy "
        }
      ],
      "final": true
    }
  ],
  "result_index": 0,
  "speaker_labels": [
    {
      "from": 0.68,
      "to": 1.19,
      "speaker": 2,
      "confidence": 0.52,
      "final": false
    },
    {
      "from": 1.47,
      "to": 1.93,
      "speaker": 1,
      "confidence": 0.62,
      "final": false
    },
    {
      "from": 1.96,
      "to": 2.12,
      "speaker": 2,
      "confidence": 0.51,
      "final": false
    },
    {
      "from": 2.12,
      "to": 2.59,
      "speaker": 2,
      "confidence": 0.51,
      "final": false
    },
    {
      "from": 2.59,
      "to": 3.17,
      "speaker": 2,
      "confidence": 0.51,
      "final": false
    },
    . . .
  ]
}
```
{: codeblock}

The `transcript` field shows the final transcript of the audio, which lists the words as they were spoken by all participants. By comparing and synchronizing the speaker labels with the timestamps, you can reassemble the conversation as it occurred.

<table style="width:50%">
  <caption>Table 1. Speaker labels example</caption>
  <tr>
    <th style="text-align:left">Timestamp</th>
    <th style="text-align:left">Speaker label</th>
  </tr>
  <tr>
    <td style="vertical-align:top">
      `"hello",`<br/>
      `0.68,`<br/>
      `1.19`
    </td>
    <td>
      `"from": 0.68,`<br/>
      `"to": 1.19,`<br/>
      `"speaker": 2,`<br/>
      `"confidence": 0.52,`<br/>
      `"final": false`
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      `"yeah",`<br/>
      `1.47,`<br/>
      `1.93`
    </td>
    <td>
      `"from": 1.47,`<br/>
      `"to": 1.93,`<br/>
      `"speaker": 1,`<br/>
      `"confidence": 0.62,`<br/>
      `"final": false`
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      `"yeah",`<br/>
      `1.96,`<br/>
      `2.12`
    </td>
    <td>
      `"from": 1.96,`<br/>
      `"to": 2.12,`<br/>
      `"speaker": 2,`<br/>
      `"confidence": 0.51,`<br/>
      `"final": false`
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      `"how's",`<br/>
      `2.12,`<br/>
      `2.59`
    </td>
    <td>
      `"from": 2.12,`<br/>
      `"to": 2.59,`<br/>
      `"speaker": 2,`<br/>
      `"confidence": 0.51,`<br/>
      `"final": false`
    </td>
  </tr>
  <tr>
    <td style="vertical-align:top">
      `"Billy",`<br/>
      `2.59,`<br/>
      `3.17`
    </td>
    <td>
      `"from": 2.59,`<br/>
      `"to": 3.17,`<br/>
      `"speaker": 2,`<br/>
      `"confidence": 0.51,`<br/>
      `"final": false`
    </td>
  </tr>
</table>

The results clearly indicate how this two-person exchange began:

-   **Speaker 2** - "Hello?"
-   **Speaker 1** - "Yeah?"
-   **Speaker 2** - "Yeah, how's Billy?"

In the example, the `confidence` fields indicate the service's confidence in its identification of the speakers. The `final` field for each word is `false`. The service sets the `final` field to `true` only for the last word of the speaker labels that it returns. A value of `true` indicates that the service has completed its analysis of the audio.

### Speaker IDs for speaker labels
{: #speakerLabelsIDs}

The example also illustrates an interesting aspect of speaker IDs. In the `speaker` fields, the first speaker has an ID of `2` and the second has an ID of `1`. The service develops a better understanding of the speaker patterns as it processes the audio. Therefore, it can change speaker IDs for individual words, and can also add and remove speakers, until it generates its final results.

As a result, speaker IDs might not be sequential, contiguous, or ordered. For instance, IDs typically start at `0`, but in the previous example the earliest ID is `1`. The service likely omitted an earlier word assignment for speaker `0` based on further analysis of the audio. Omissions can happen for later speakers, as well. Omissions can result in gaps in the numbering and produce results for two speakers who are labeled, for example, `0` and `2`. Another consideration is that speakers can leave a conversation. So a participant who contributes only to the early stages of a conversation might not appear in later results.

### Requesting interim results for speaker labels
{: #speakerLabelsInterim}

With the WebSocket interface, you can request interim results as well as speaker labels (for more information, see [Interim results](/docs/speech-to-text?topic=speech-to-text-output#interim)). Final results are generally better than interim results. But interim results can help identify the evolution of a transcript and the assignment of speaker labels. Interim results can indicate where transient speakers and IDs appeared or disappeared. However, the service can reuse the IDs of speakers that it initially identifies and later reconsiders and omits. Therefore, an ID might refer to two different speakers in interim and final results.

When you request both interim results and speaker labels, final results for long audio streams might arrive well after initial interim results are returned. It is also possible for some interim results to include only a `speaker_labels` field without the `results` and `result_index` fields for the transcript as a whole. If you do not request interim results, the service returns final results that include `results` and `result_index` fields and a single `speaker_labels` field.

With interim results, a `final` field with a value of `false` for a word in the `speaker_labels` field can indicate that the service is still processing the audio. The service might change its identification of the speaker or its confidence for individual words before it is done. The service sends final results when the audio stream is complete or in response to a timeout, whichever occurs first. The service sets the `final` field to `true` only for the last word of the speaker labels that it returns in either case.

### Performance considerations for speaker labels
{: #speakerLabelsPerformance}

As noted previously, the speaker labels feature is optimized for two-person conversations, such as communications with a call center. Therefore, you need to consider the following potential performance issues:

-   Performance for audio with a single speaker can be poor. Variations in audio quality or in the speaker's voice can cause the service to identify extra speakers who are not present. Such speakers are referred to as hallucinations.
-   Similarly, performance for audio with a dominant speaker, such as a podcast, can be poor. The service tends to miss speakers who talk for shorter amounts of time, and it can also produce hallucinations.
-   Performance for audio with more than six speakers is undefined. The feature can handle a maximum of six speakers.
-   Performance for short utterances can be less accurate than for long utterances. The service produces better results when participants speak for longer amounts of time, at least 30 seconds per speaker. The relative amount of audio that is available for each speaker can also affect performance.
-   Performance can degrade for the first 30 seconds of speech. It usually improves to a reasonable level after 1 minute of audio, as the service receives more data to work with.

As with all transcription, performance can also be affected by poor audio quality, background noise, a person's manner of speech, overlapping speakers, and other aspects of the audio. {{site.data.keyword.IBM_notm}} continues to refine and improve the performance of the speaker labels feature. For more information about the latest improvements, see [IBM Research AI Advances Speaker Diarization in Real Use Cases](https://www.ibm.com/blogs/research/2020/07/speaker-diarization-in-real-use-cases/){: external}.

## Keyword spotting
{: #keyword_spotting}

The keyword spotting feature detects specified strings in a transcript. The service can spot the same keyword multiple times and report each occurrence. The service spots keywords only in the final results, not in interim results. By default, the service does no keyword spotting.

To use keyword spotting, you must specify both of the following parameters:

-   Use the `keywords` parameter to specify an array of strings to be spotted. The service spots no keywords if you omit the parameter or specify an empty array. A keyword string can include more than one token. For example, the keyword `Speech to Text` has three tokens. Keyword matching is case-insensitive, so `Speech to Text` is effectively equivalent to `speech to text`.

    For US English, the service normalizes each keyword to match spoken versus written strings. For example, it normalizes numbers to match how they are spoken as opposed to written. For other languages, keywords must be specified as they are spoken.
-   Use the `keywords_threshold` parameter to specify a probability between 0.0 and 1.0 for a keyword match. The threshold indicates the lower bound for the level of confidence the service must have for a word to match the keyword. A keyword is spotted in the transcript only if its confidence is greater than or equal to the specified threshold.

    Specifying a small threshold can potentially produce many matches. If you specify a threshold, you must also specify one or more keywords. Omit the parameter to return no matches.

The following limits apply to keyword spotting:

-   You can spot a maximum of 1000 keywords with a single request.
-   A single keyword can have a maximum length of 1024 characters. The maximum effective length for double-byte languages might be shorter.
-   Most HTTP servers and proxies impose a limit of 8 KB on the parameters of a request. Spotting a very large number of keywords or many lengthy keywords can exceed this limit. If you need to match more keywords, consider using a [multipart HTTP request](/docs/speech-to-text?topic=speech-to-text-http#HTTP-multi).

Keyword spotting is necessary to identify keywords in an audio stream. You cannot identify keywords by processing a final transcript because the transcript represents the service's best decoding results for the input audio. It does not include tokens with lower confidence scores that might represent a word of interest. So applying text processing tools to a transcript on the client side might not identify keywords. A richer representation of decoding results is needed, and that representation is available only at the server.

### Keyword spotting results
{: #keywordSpottingResults}

The service returns the results in a `keywords_result` field that is an element of the `results` array. The `keywords_result` field is a dictionary, or associative array, of enumerable properties. Each property is identified by a specified keyword and includes an array of objects. The service returns one element of the array for each match that it finds for the keyword. The object for each match includes the following fields:

-   `normalized_text` is the specified keyword that is normalized to the spoken phrase that matched in the input audio.
-   `start_time` is the start time in seconds of the match.
-   `end_time` is the end time in seconds of the match.
-   `confidence` is the service's confidence that the match represents the specified keyword. The confidence must be at least as great as the specified threshold to be included in the results.

A keyword for which the service finds no matches is omitted from the array. A keyword might not be found if

-   The audio simply does not include the keyword. Absence of the keyword is the most obvious explanation.
-   The threshold is set too high. The service might identify the keyword but with a lower level of confidence, in which case it omits the match from the results.
-   A keyword string that contains multiple tokens (for example, `Speech to Text`) is spoken with too much silence between its tokens. When the service transcribes audio, it chops the stream into a series of blocks. Each block represents a continuous chunk of audio that does not have an interval of silence that exceeds half of a second. It constructs an array of result objects that consists of these blocks.

    The service matches a multi-token keyword only if

    -   The keyword's tokens are in the same block.
    -   The tokens are either adjacent or separated by a gap of no more than 0.1 seconds.

    The latter case can occur if a brief filler or non-lexical utterance, such as "uhm" or "well," lies between two tokens of the keyword. For more information, see [Hesitation markers](/docs/speech-to-text?topic=speech-to-text-basic-response#hesitation).

### Keyword spotting example
{: #keywordSpottingExample}

The following example request sets the `keywords` parameter to a URL-encoded array of three strings (`colorado`, `tornado`, and `tornadoes`) and the `keywords_threshold` parameter to `0.5`. The service finds qualifying occurrences of `colorado` and `tornadoes`.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/flac" \
--data-binary @{path}audio-file.flac \
"{url}/v1/recognize?keywords=colorado%2Ctornado%2Ctornadoes&keywords_threshold=0.5"
```
{: pre}

```javascript
{
  "results": [
    {
      "keywords_result": {
        "colorado": [
          {
            "normalized_text": "Colorado",
            "start_time": 4.94,
            "confidence": 0.91,
            "end_time": 5.62
          }
        ],
        "tornadoes": [
          {
            "normalized_text": "tornadoes",
            "start_time": 1.52,
            "confidence": 1.0,
            "end_time": 2.15
          }
        ]
      },
      "alternatives": [
        {
          "confidence": 0.96,
          "transcript": "several tornadoes touch down as a line of
severe thunderstorms swept through Colorado on Sunday "
        }
      ],
      "final": true
    }
  ],
  "result_index": 0
}
```
{: codeblock}

## Maximum alternatives
{: #max_alternatives}

The `max_alternatives` parameter accepts an integer value that tells the service to return the *n*-best alternative hypotheses for the results. By default, the service returns only a single transcription result, which is equivalent to setting the parameter to `1`. By setting `max_alternatives` to a number greater than 1, you ask the service to return that number of the best alternative transcriptions. (If you specify a value of `0`, the service uses the default value of `1`.)

The service reports a confidence score only for the best alternative that it returns. In most cases, that is the alternative to choose.

### Maximum alternatives example
{: #maximumAlternativesExample}

The following example request sets the `max_alternatives` parameter to `3`; the service reports a confidence for the most likely of the three alternatives.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/flac" \
--data-binary @{path}audio-file.flac \
"{url}/v1/recognize?max_alternatives=3"
```
{: pre}

```javascript
{
  "results": [
    {
      "alternatives": [
        {
          "confidence": 0.96,
          "transcript": "several tornadoes touch down as a line of
severe thunderstorms swept through Colorado on Sunday "
        },
        {
          "transcript": "several tornadoes touched down as a line of
severe thunderstorms swept through Colorado on Sunday "
        },
        {
          "transcript": "several tornadoes touch down as a line of
severe thunderstorms swept through Colorado and Sunday "
        }
      ],
      "final": true
    }
  ],
  "result_index": 0
}
```
{: codeblock}

## Interim results
{: #interim}

The interim results feature is available only with the WebSocket interface.
{: note}

Interim results are intermediate hypotheses of a transcription that are likely to change before the service returns its final results. The service returns interim results as soon as it generates them. Interim results are useful for

-   Interactive applications and real-time transcription
-   Long audio streams, which can take a while to transcribe

Interim results arrive more often and more quickly than final results. You can use them to enable your application to respond more quickly or to gauge the progress of the transcription.

To receive interim results, set the `interim_results` JSON parameter to `true` in the `start` message for a WebSocket recognition request. If you omit the `interim_results` parameter or set it to `false`, the service returns only a single JSON transcript at the end of the audio. Follow these guidelines to use the parameter:

-   Omit the parameter or set it to `false` if you are doing offline or batch transcription, do not need minimum latency, and want a single JSON result for all audio.
-   Set the parameter to `true` if you want results to arrive progressively as the service processes the audio or if you want the results with minimum latency. Keep in mind that the service can update interim results as it processes more audio.

Interim results are indicated in the JSON response with the `final` field set to `false`. The service can update such results with more accurate transcriptions as it processes further audio. Final results are identified with the `final` field set to `true`. The service makes no further updates to final results.

### Interim results example
{: #interimResultsExample}

The following abbreviated example requests interim results for a WebSocket request. In its response, the service sets the `final` attribute to `true` only for the final result.

```javascript
var IAM_access_token = {access_token};
var wsURI = '{ws_url}/v1/recognize'
  + '?access_token=' + IAM_access_token;
var websocket = new WebSocket(wsURI);

websocket.onopen = function(evt) { onOpen(evt) };
function onOpen(evt) {
  var message = {
    action: 'start',
    content-type: 'audio/l16;rate=22050',
    interim_results: true
  };
  websocket.send(JSON.stringify(message));
  websocket.send(blob);
}

websocket.onmessage = function(evt) { onMessage(evt) };
function onMessage(evt) {
  console.log(evt.data);
}
```
{: codeblock}

```javascript
{
  "results": [
    {
      "alternatives": [
        {
          "transcript": "several to "
        }
      ],
      "final": false
    }
  ],
  "result_index": 0
}{
  "results": [
    {
      "alternatives": [
        {
          "transcript": "several tornadoes "
        }
      ],
      "final": false
    }
  ],
  "result_index": 0
}{
  . . .
}{
  "results": [
    {
      "alternatives": [
        {
          "transcript": "several tornadoes touch down as a line of
severe thunderstorms swept through Colorado on Sunday "
        }
      ],
      "final": false
    }
  ],
  "result_index": 0
}{
  "results": [
    {
      "alternatives": [
        {
          "confidence": 0.96,
          "transcript": "several tornadoes touch down as a line of
severe thunderstorms swept through Colorado on Sunday "
        }
      ],
      "final": true
    }
  ],
  "result_index": 0
}
```
{: codeblock}

## Word alternatives
{: #word_alternatives}

The word alternatives feature (also known as Confusion Networks) reports hypotheses for acoustically similar alternatives for words of the input audio. For instance, the word `Austin` might be the best hypothesis for a word from the audio. But the word `Boston` is another possible hypothesis in the same time interval. Hypotheses share a common start and end time but have different spellings and usually different confidence scores.

By default, the service does not report word alternatives. To indicate that you want to receive alternative hypotheses, you use the `word_alternatives_threshold` parameter to specify a probability between 0.0 and 1.0. The threshold indicates the lower bound for the level of confidence the service must have in a hypothesis to return it as a word alternative. A hypothesis is returned only if its confidence is greater than or equal to the specified threshold.

You can think of word alternatives as the timeline for a transcript that is chopped into smaller intervals, or bins. Each bin can have one or more hypotheses with different spellings and confidence. The `word_alternatives_threshold` parameter controls the density of the results that the service returns. Specifying a small threshold can potentially produce many hypotheses.

### Word alternatives results
{: #wordAlternativesResults}

The service returns the results in a `word_alternatives` field that is an element of the `results` array. The `word_alternatives` field is an array of objects, each of which provides the following fields for an alternative word:

-   `start_time` indicates the time in seconds at which the word for which hypotheses are returned starts in the input audio.
-   `end_time` indicates the time in seconds at which the word for which hypotheses are returned ends in the input audio.
-   `alternatives` is an array of hypothesis objects. Each object includes a `confidence` that indicates the service's confidence score for the hypothesis and a `word` that identifies the hypothesis. The confidence must be at least as great as the specified threshold to be included in the results. The service orders the alternatives by confidence.

### Word alternatives example
{: #wordAlternativesExample}

The following example request specifies a rather low `word_alternatives_threshold` of `0.10`. The simple input audio includes words with common homonyms and different possible acoustic interpretations. Because of the low threshold, the service returns multiple hypotheses and confidence scores for some of the words, along with the start and end times of all words. The final transcript correctly recognizes the input audio.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/wav" \
--data-binary @{path}audio-file.wav \
"{url}/v1/recognize?word_alternatives_threshold=0.10"
```
{: pre}

```javascript
{
   "result_index": 0,
   "results": [
      {
         "final": true,
         "alternatives": [
            {
               "transcript": "yes I ate that tuna ",
               "confidence": 0.82
            }
         ],
         "word_alternatives": [
            {
               "start_time": 0.0,
               "end_time": 0.31,
               "alternatives": [
                  {
                     "word": "yes",
                     "confidence": 1.0
                  }
               ]
            },
            {
               "start_time": 0.31,
               "end_time": 0.46,
               "alternatives": [
                  {
                     "word": "I",
                     "confidence": 1.0
                  }
               ]
            },
            {
               "start_time": 0.46,
               "end_time": 0.63,
               "alternatives": [
                  {
                     "word": "ate",
                     "confidence": 0.89
                  },
                  {
                     "word": "eat",
                     "confidence": 0.11
                  }
               ]
            },
            {
               "start_time": 0.63,
               "end_time": 0.77,
               "alternatives": [
                  {
                     "word": "that",
                     "confidence": 0.72
                  },
                  {
                     "word": "the",
                     "confidence": 0.27
                  }
               ]
            },
            {
               "start_time": 0.77,
               "end_time": 1.2,
               "alternatives": [
                  {
                     "word": "tuna",
                     "confidence": 0.94
                  }
               ]
            }
         ]
      }
   ]
}
```
{: codeblock}

## Word confidence
{: #word_confidence}

The `word_confidence` parameter tells the service whether to provide confidence measures for the words of the transcript. By default, the service reports a confidence measure only for the final transcript as a whole. Setting `word_confidence` to `true` directs the service to report a confidence measure for each individual word of the transcript.

A confidence measure indicates the service's estimation that the transcribed word is correct based on the acoustic evidence. Confidence scores range from 0.0 to 1.0.

-   A score of 1.0 indicates that the current transcription of the word reflects the most likely result.
-   A score of 0.5 means that the word has a 50-percent chance of being correct.

### Word confidence example
{: #wordConfidenceExample}

The following example requests word confidence scores for the words of the transcription.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/flac" \
--data-binary @{path}audio-file.flac \
"{url}/v1/recognize?word_confidence=true"
```
{: pre}

```javascript
{
  "results": [
    {
      "alternatives": [
        {
          "confidence": 0.96,
          "transcript": "several tornadoes touch down as a line of
severe thunderstorms swept through Colorado on Sunday ",
          "word_confidence": [
            [
              "several",
              1.0
            ],
            [
              "tornadoes",
              1.0
            ],
            [
              "touch",
              0.52
            ],
            [
              "down",
              0.90
            ],
            . . .
            [
              "on",
              0.31
            ],
            [
              "Sunday",
              0.99
            ]
          ]
        }
      ],
      "final": true
    }
  ],
  "result_index": 0
}
```
{: codeblock}

## Word timestamps
{: #word_timestamps}

The `timestamps` parameter tells the service whether to produce timestamps for the words it transcribes. By default, the service reports no timestamps. Setting `timestamps` to `true` directs the service to report the beginning and ending time in seconds for each word relative to the start of the audio.

### Word timestamps example
{: #wordTimestampsExample}

The following example requests timestamps for the words of the transcription.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/flac" \
--data-binary @{path}audio-file.flac \
"{url}/v1/recognize?timestamps=true"
```
{: pre}

```javascript
{
  "results": [
    {
      "alternatives": [
        {
          "timestamps": [
            [
              "several",
              1.01,
              1.52
            ],
            [
              "tornadoes",
              1.52,
              2.15
            ],
            [
              "touch",
              2.15,
              2.5
            ],
            [
              "down",
              2.5,
              2.81
            ],
            . . .
            [
              "on",
              5.62,
              5.74
            ],
            [
              "Sunday",
              5.74,
              6.34
            ]
          ],
          "confidence": 0.96,
          "transcript": "several tornadoes touch down as a line of
severe thunderstorms swept through Colorado on Sunday "
        }
      ],
      "final": true
    }
  ],
  "result_index": 0
}
```
{: codeblock}

## Smart formatting
{: #smart_formatting}

The smart formatting feature is beta functionality that is available for US English, Spanish, and Japanese.
{: beta}

The `smart_formatting` parameter directs the service to convert the following strings into more conventional representations:

-   Dates
-   Times
-   Series of digits and numbers
-   Phone numbers
-   Currency values (for US English and Spanish)
-   Internet email and web addresses (for US English and Spanish)

You set the `smart_formatting` parameter to `true` to enable smart formatting. By default, the service does not perform smart formatting. The service applies smart formatting just before it returns the final results to the client, when text normalization is complete. The conversion makes the transcript more readable and promotes better post-processing of transcription results by representing these artifacts as they would normally be written.

### What results does smart formatting affect?
{: #smartFormattingEffects}

Smart formatting impacts some transcription results and not others:

-   Smart formatting affects only words in the `transcript` field of final results, those results for which the `final` field is `true`. It does not affect interim results, for which `final` is `false`.
-   Smart formatting does not affect words in other fields of the response. For example, smart formatting is not applied to response data in the `timestamps` or `alternatives` fields.
-   Hesitation markers and disfluencies, such as "uhm" and "uh", can adversely impact the conversion of phrases and strings by smart formatting. Therefore, smart formatting suppresses hesitation markers from the `transcript` field for final results. Hesitation markers continue to appear in interim results. For more information, see [Hesitation markers](/docs/speech-to-text?topic=speech-to-text-basic-response#hesitation).

### Language differences
{: #smartFormattingDifferences}

Smart formatting is based on the presence of obvious keywords in the transcript. Because of differences among the supported languages, smart formatting works slightly differently for each language. The following sections describe the strings and content that trigger smart formatting changes for [US English and Spanish](#smartFormattingEnglishSpanish) and for [Japanese](#smartFormattingJapanese).

#### US English and Spanish
{: #smartFormattingEnglishSpanish}

-   Times are identified by keywords such as `AM`, `PM`, or `EST`.
-   Military times are converted if they are identified by the keyword `hours` (*US English*) or `horas` (*Spanish*).
-   Phone numbers must be either `911` or a number that contains 10 or 11 digits and starts with the number `1`.
-   Currency symbols are substituted for the following strings in appropriate contexts:
    -   *For US English,* dollar, cent, and euro.
    -   *For Spanish,* dolar, peso, peseta, libras esterlinas, libra, and euro.
-   Internet email addresses are converted in some cases. Specifically, the service converts email addresses if the input audio uses the phrasing `email address ... {address}`. The following examples show a correct conversion of spoken phrases:
    -   `My email address is j dot d o e at i b m dot com` becomes `My email address is j.doe@ibm.com`.
    -   `Mi correo electronico es j punto d o e arroba i b m punto com` becomes `Mi correo electronico es j.doe@ibm.com`.
-   Internet web addresses are converted in their short forms. Fully qualified web addresses are not converted. The following examples show complete conversions:
    -   `I saw the story on yahoo dot com` becomes `I saw the story on yahoo.com`.
    -   `Vi la historia en yahoo punto com` becomes `Vi la historia en yahoo.com`.

    The following examples show incomplete conversions:
    -   `I saw the story on w w w dot yahoo dot com` becomes `I saw the story on w w w .yahoo.com`.
    -   `Vi la historia en w w w punto yahoo punto com` becomes `Vi la historia en w w w .yahoo.com`.
-   Conversion of large numbers and currency values can be challenging. The service converts digits and many numbers well. But larger and more complex numbers and currency values work best with more precise phrasing. For example, the service correctly converts the following transcripts because of their precise wording:
    -   `sixty nine thousand five hundred sixty dollars and twenty five cents` becomes `$69560.25`,
    -   `sixty nine thousand five hundred sixty dollars point twenty five` becomes `$69560.25`.

    But the service cannot correctly convert the following transcripts due to their looser phrasing:
    -   `sixty nine thousand five sixty dollars and twenty five cents` becomes `60 9000 $560.25`.
    -   `sixty nine thousand five sixty dollars point twenty five` becomes `60 9000 $560.25`.

    To correctly convert a greater possible variety of complex numbers, you need to experiment with the results of smart formatting and customize your own post-processing utilities.
-   *For US English,* certain punctuation symbols are added for special keywords that occur in appropriate places. The service substitutes punctuation symbols for the following keyword strings based on where it finds them in a transcript.

    <table style="width:50%">
      <caption>Table 2. Smart formatting punctuation keywords for US English</caption>
      <tr>
        <th style="width:45%; text-align:left">Keyword string</th>
        <th style="text-align:center">Resulting punctuation</th>
      </tr>
      <tr>
        <td>
          `Comma`
        </td>
        <td style="text-align:center">
          `,`
        </td>
      </tr>
      <tr>
        <td>
          `Period`
        </td>
        <td style="text-align:center">
          `.`
        </td>
      </tr>
      <tr>
        <td>
          `Question mark`
        </td>
        <td style="text-align:center">
          `?`
        </td>
      </tr>
      <tr>
        <td>
          `Exclamation point`
        </td>
        <td style="text-align:center">
          `!`
        </td>
      </tr>
    </table>

    The service converts these keyword strings to symbols only in appropriate positions of a transcript. In the following example, the speaker says the word `period` at the end of the sentence. The service correctly differentiates between the noun that appears earlier in the sentence and the concluding punctuation.
    -   `the warranty period is short period` becomes `the warranty period is short.`

#### Japanese
{: #smartFormattingJapanese}

-   Phone numbers must be 10 or 11 digits and begin with valid prefixes for telephone numbers in Japan. For example, valid prefixes include `03` and `090`.
-   English words are converted to ASCII (*hankaku*) characters. For example, <code>&#65321;&#65314;&#65325;</code> is converted to `IBM`.
-   Ambiguous terms might not be converted if sufficient context is unavailable. For example, it is unclear whether <code>&#19968;&#26178;</code> and <code>&#21313;&#20998;</code> refer to times.
-   Punctuation is handled in the same way with or without smart formatting. For example, based on probability calculations, one of <code>&#12459;&#12531;&#12510;</code> or `,` is selected.
-   Strings that describe yen values are not replaced with the yen currency symbol.
-   Internet email and web addresses in any form are not converted.
-   The Japanese narrowband model (`ja-JP_NarrowbandModel`) includes some multigram word units for digits and decimal fractions. The service returns these multigram units regardless of whether you enable smart formatting. The following examples show the units that the service returns. The numbers in parentheses show the equivalent Arabic numeric expression for each unit.
    -   *Digits:* <code>&#12295;&#19968;</code> (01), ..., <code>&#12295;&#20061;</code> (09), <code>&#19968;&#12295;</code> (10), ..., <code>&#20061;&#12295;</code> (90)
    -   *Decimal fractions:* <code>&#12295;&#12539;</code> (0.), <code>&#19968;&#12539;</code> (1.), ..., <code>&#21313;&#12539;</code> (10.)

    The smart formatting feature understands and returns the multigram units that the model generates. If you apply your own post-processing to transcription results, you need to handle these units appropriately.

### Smart formatting results
{: #smartFormattingResults}

The following table shows examples of final transcripts both with and without smart formatting. The transcripts are based on US English audio.

| Information | Without smart formatting | With smart formatting |
|-------------|--------------------------|-----------------------|
| Dates | I was born on ten oh six nineteen seventy | I was born on 10/6/1970 |
|       | I was born on the ninth of December nineteen hundred | I was born on 12/9/1900 |
|       | Today is June sixth | Today is June 6 |
| Times | The meeting starts at nine thirty AM | The meeting starts at 9:30 AM |
|       | I am available at seven EST | I am available at 7:00 EST |
|       | We meet at oh seven hundred hours | We meet at 0700 hours |
| Numbers | The quantity is one million one hundred and one | The quantity is 1000101 |
|         | One point five is between one and two | 1.5 is between 1 and 2 |
| Phone numbers | Call me at nine one four two three seven one thousand | Call me at 914-237-1000 |
|               | Call me at one nine one four nine oh nine twenty six forty five | Call me at 1-914-909-2645 |
| Currency values | You owe me three thousand two hundred two dollars and sixty six | You owe me $3202.66 |
|                 | The dollar rose to one hundred and nine point seven nine yen from one hundred and nine point seven two yen | The dollar rose to 109.79 yen from 109.72 yen |
| Internet email and web addresses | My email address is john dot doe at foo dot com | My email address is john.doe@foo.com |
|                                  | I saw the story on yahoo dot com | I saw the story on yahoo.com |
| Combinations | The code is zero two four eight one and the date of service is May fifth two thousand and one | The code is 02481 and the date of service is 5/5/2001 |
|              | There are forty seven links on Yahoo dot com now | There are 47 links on Yahoo.com now |
{: caption="Table 3. Smart formatting example transcripts"}

### Smart formatting results for long pauses
{: #smartFormattingLongPauses}

In cases where an utterance contains long enough pauses of silence, the service can split the transcript into two or more final results. This affects the contents of the response, as shown in the following examples.

| Audio speech | Formatted transcription results |
|--------------|---------------------------------|
| My phone number is nine one four five five seven three three nine two | "My phone number is 914-557-3392" |
| My phone number is nine one four *&lt;pause&gt;* five five seven three three nine two | "My phone number is 914"<br/>"5573392" |
{: caption="Table 4. Smart formatting example transcripts for long pauses"}

For more information about specifying a pause interval that affects the service's response, see [End of phrase silence time](/docs/speech-to-text?topic=speech-to-text-output#silence_time).

### Smart formatting example
{: #smartFormattingExample}

The following example requests smart formatting with a recognition request by setting the `smart_formatting` parameter to `true`. The following sections show the effects of smart formatting on the results of a request.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/flac" \
--data-binary @{path}audio-file.flac \
"{url}/v1/recognize?smart_formatting=true"
```
{: pre}

## Numeric redaction
{: #redaction}

The numeric redaction feature is beta functionality that is available for US English, Japanese, and Korean.
{: beta}

The `redaction` parameter directs the service to redact, or mask, numeric data from final transcripts. The feature redacts any number that has three or more consecutive digits by replacing each digit with an `X` character. It is intended to redact sensitive numeric data, such as credit card numbers.

By default, the service does not redact numeric data. Set the `redaction` parameter to `true` to enable numeric redaction. When you enable redaction, the service automatically enables smart formatting, regardless of whether you explicitly disable that feature. To ensure maximum security, the service also enforces the following parameter values when you enable redaction:

-   The service disables keyword spotting, regardless of whether you specify values for the `keywords` and `keywords_threshold` parameters.
-   The service disables interim results, regardless of whether you set the `interim_results` parameter of the WebSocket interface to `true`.
-   The service returns only a single, final transcript, regardless of whether you specify a value for the `maximum_alternatives` parameter.

The design of the feature parallels the existing smart formatting feature. The service applies redaction only to the final transcript of a recognition request, just before it returns the results to the client and after text normalization is complete.

Future versions of the feature might expand redaction to mask all telephone numbers, street addresses, bank accounts, social security numbers, and other sensitive numeric data.
{: note}

### Language differences
{: #redactionDifferences}

The feature works exactly as described for US English models but has the following differences for Japanese and Korean models.

#### Japanese
{: #redactionJapanese}

Japanese redaction has the following differences:

-   In addition to masking strings of three or more consecutive digits, redaction also masks street addresses and numbers, regardless of whether they contain fewer than three digits.
-   Similarly, redaction also masks date information in Japanese-style birth dates. In Japanese, date information is usually presented in Common Era format but sometimes follows Japanese style, particularly for birth dates. In this case, the year and month are masked even though they contain just one or two digits. For example, numeric redaction changes the following string as shown.

    <table style="width:50%">
      <caption>Table 5. Example redaction of Japanese-style birth date</caption>
      <tr>
        <th style="text-align:left">Without redaction</th>
        <th style="text-align:left">With redaction</th>
      </tr>
      <tr>
        <td>
          &#24179;&#25104; 30&#24180; 2&#26376;
        </td>
        <td>
          &#24179;&#25104; XX&#24180; X&#26376;
        </td>
      </tr>
    </table>

#### Korean
{: #redactionKorean}

Korean redaction has the following differences:

-   The smart formatting feature is not supported. The service still performs numeric redaction for Korean, but it performs no other smart formatting.
-   Isolated digit characters are reduced, but possible digit characters that are included as part of Korean phrases are not. For example, the character <code>&#51060;</code> in the following phrase is not replaced by an `X` because it is adjacent to the following character:

    <code>&#51060;&#51077;&#45768;&#45796;</code>

    If the <code>&#51060;</code> character were separated from the following character by a space, it would be replaced by an `X`, as described in [Numeric redaction results](#redactionResults).

### Numeric redaction results
{: #redactionResults}

The following table shows examples of final transcripts both with and without numeric redaction in each supported language.

| Language | Without redaction | With redaction |
|----------|-------------------|----------------|
| US English | my credit card number is four one four seven two | my credit card number is XXXXX |
| Japanese | &#31169; &#12398;&#12463;&#12524;&#12472;&#12483;&#12488; &#12459;&#12540;&#12489; &#30058;&#21495; &#12399; &#22235; &#19968; &#22235; &#19971; &#20108;&#12391;&#12377; | &#31169; &#12398;&#12463;&#12524;&#12472;&#12483;&#12488; &#12459;&#12540;&#12489; &#30058;&#21495; &#12399; XXXXX &#12391;&#12377; |
| Korean | &#45236; &#49888;&#50857; &#52852;&#46300; &#48264;&#54840;&#45716; &#49324; &#51068; &#49324; &#52832; &#51060; &#48264;&#51077;&#45768;&#45796; | &#45236; &#49888;&#50857; &#52852;&#46300; &#48264;&#54840;&#45716; XXXXX &#48264;&#51077;&#45768;&#45796; |
{: caption="Table 6. Numeric redaction example transcripts"}

### Numeric redaction example
{: #redactionExample}

The following example requests numeric redaction with a recognition request by setting the `redaction` parameter to `true`. Because the request enables redaction, the service implicitly enables smart formatting with the request. The service effectively disables the other parameters of the request so that they have no effect: The service returns a single final transcript and recognizes no keywords.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/wav" \
--data-binary @{path}audio-file.wav \
"{url}/v1/recognize?&redaction=true&max_alternatives=3&keywords=birth%2Cbirthday&keywords_threshold=0.5"
```
{: pre}

## Profanity filtering
{: #profanity_filter}

The profanity filtering feature is generally available for US English and Japanese only.
{: note}

The `profanity_filter` parameter indicates whether the service is to censor profanity from its results. By default, the service obscures all profanity by replacing it with a series of asterisks in the transcript. Setting the parameter to `false` displays words in the output exactly as transcribed.

The service censors profanity from all final transcripts and from any alternative transcripts. It also censors profanity from results that are associated with word alternatives, word confidence, and word timestamps. The sole exception is keyword spotting, for which the service returns all words as specified by the user, regardless of whether `profanity_filter` is `true`.

### Profanity filtering example
{: #profanityFilteringExample}

The following example shows the results for a brief audio file that is transcribed with the default `true` value for the `profanity_filter` parameter. The request also sets the `word_alternatives_threshold` parameter to a relatively high value of `0.99` and the `word_confidence` and `timestamps` parameters to `true`.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/flac" \
--data-binary @{path}audio-file.flac \
"{url}/v1/recognize?word_alternatives_threshold=0.99&word_confidence=true&timestamps=true"
```
{: pre}

```javascript
{
  "results": [
    {
      "word_alternatives": [
        {
          "start_time": 0.03,
          "alternatives": [
            {
              "confidence": 1.0,
              "word": "****"
            }
          ],
          "end_time": 0.25
        },
        {
          "start_time": 0.25,
          "alternatives": [
            {
              "confidence": 0.99,
              "word": "you"
            }
          ],
          "end_time": 0.56
        }
      ],
      "alternatives": [
        {
          "transcript": "**** you",
          "confidence": 0.99,
          "word_confidence": [
            ["****", 1.0],
            ["you", 0.99]
          ],
          "timestamps": [
            ["****", 0.03, 0.25],
            ["you", 0.25, 0.56]
          ]
        }
      ],
      "final": true
    }
  ],
  "result_index": 0
}
```
{: codeblock}

## End of phrase silence time
{: #silence_time}

The `end_of_phrase_silence_time` parameter specifies the duration of the pause interval at which the service splits a transcript into multiple final results. If the service detects pauses or extended silence before it reaches the end of the audio stream, its response can include multiple final results. Silence indicates a point at which the speaker pauses between spoken words or phrases. For most languages, the default pause interval is 0.8 seconds; for Chinese the default interval is 0.6 seconds. For more information, see [Pauses and silence](/docs/speech-to-text?topic=speech-to-text-basic-response#pauses-silence).

By using the `end_of_phrase_silence_time` parameter, you can specify a double value between 0.0 and 120.0 seconds that indicates a different pause interval:

-   A value greater than 0 specifies the interval that the service is to use for speech recognition.
-   A value of 0 indicates that the service is to use the default interval. It is equivalent to omitting the parameter.

You can use the parameter to strike a balance between how often a final result is produced and the accuracy of the transcription:

-   A longer pause interval produces fewer final results, and each result covers a larger segment of audio. Larger segments tend to be more accurate because the service has more context with which to transcribe the audio.

    However, larger pause intervals directly impact the latency of the final results. After the last word of the input audio, the service must wait for the indicated interval to expire before it returns its response. The service waits to ensure that the input audio does not continue beyond the longer interval. (With the WebSocket interface, setting the parameter can affect the latency and accuracy of the final results, regardless of whether you request interim results.)
-   A shorter pause interval yields more final results, but because each segment of audio is smaller, transcription accuracy might not be as good. Smaller segments are acceptable when latency is crucial and transcription accuracy is not expected to degrade or any degradation is acceptable.

    Also, a multi-phrase grammar recognizes, or matches, responses only within a single final result. If you use a grammar to recognize multiple strings, you can increase the pause interval to avoid receiving multiple final results.

Increase the interval when accuracy is more important than latency. Decrease the interval when the speaker is expected to say short phrases or single-word responses.

### End of phrase silence time example
{: #silence_time_example}

The following example requests show the effect of using the `end_of_phrase_silence_time` parameter. The audio speaks the phrase "one two three four five six," with a one-second pause between the words "three" and "four." The speaker might be reading a six-digit number from an identification card, for example, and pause to confirm the number.

The first example uses the default pause interval of 0.8 seconds. Because the pause is greater than the default interval, the service splits the transcript at the pause. The `confidence` of both results is `0.99`, so transcription accuracy is very good despite the pause.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/wav" \
--data-binary @{path}audio-file.wav \
"{url}/v1/recognize"
```
{: pre}

```javascript
{
  "results": [
    {
      "alternatives": [
        {
          "confidence": 0.99,
          "transcript": "one two three "
        }
      ],
      "final": true
    },
    {
      "alternatives": [
        {
          "confidence": 0.99,
          "transcript": "four five six "
        }
      ],
      "final": true
    }
  ],
  "result_index": 0
}
```
{: codeblock}

But suppose speech recognition is performed with a grammar that expects the user to speak six digits in a single-phrase response. Because the service splits the transcript at the one-second pause, the results are empty. The grammar is applied to each final result, but neither result, "one two three" nor "four five six", contains six digits.

The second example uses the same audio but sets the `end_of_phrase_silence_time` to 1.5 seconds. Because this value is greater than the length of the speaker's pause, the service returns a single final result that contains the entire spoken phrase. A grammar that expects to find six digits recognizes this transcript.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/wav" \
--data-binary @{path}audio-file.wav \
"{url}/v1/recognize?end_of_phrase_silence_time=1.5"
```
{: pre}

```javascript
{
  "results": [
    {
      "alternatives": [
        {
          "confidence": 1.0,
          "transcript": "one two three four five six "
        }
      ],
      "final": true
    }
  ],
  "result_index": 0
}
```
{: codeblock}

## Split transcript at phrase end
{: #split_transcript}

The `split_transcript_at_phrase_end` parameter directs the service to split the transcript into multiple final results based on semantic features of the input. Setting the parameter to `true` causes the service to split the transcript at the conclusion of meaningful phrases such as sentences. The service bases its understanding of semantic features on the base language model that you use with the request along with a custom language model or grammar that you use. Custom language models and grammars can influence how and where the service splits a transcript.

If you apply a custom language model to speech recognition, the service is likely to split the transcript based on the content and nature of the model's corpora. For example, a model whose corpora include many short sentences biases the service to mimic the corpora and split the input into multiple, shorter final results. Similarly, a grammar that expects a series of six digits can influence the service to insert splits to accommodate that series of digits.

However, the degree to which custom language models and grammars influence the service's splitting of a transcript depends on many factors. Such factors include the amount of training data with which the custom model is built and the qualities of the audio itself. Small changes in the audio can affect the results.

Regardless, the service can still produce multiple final results in response to pauses and silence. If you omit the `split_transcript_at_phrase_end` parameter or set it to `false`, the service splits transcripts based solely on the pause interval. The pause interval has precedence over splitting due to semantic features. For more information, see [End of phrase silence time](/docs/speech-to-text?topic=speech-to-text-output#silence_time).

### Split transcript at phrase end results
{: #split_transcript_results}

When the split transcript at phrase end feature is enabled, each final result in the transcript includes an additional `end_of_utterance` field that identifies why the service split the transcript at that point:

-   `full_stop` - A full semantic stop, such as for the conclusion of a grammatical sentence. The insertion of splits is influenced by the base language model and biased by custom language models and grammars, as described previously.
-   `silence` - A pause or silence that is at least as long as the pause interval. You can use the `end_of_phrase_silence_time` parameter to control the length of the pause interval.
-   `end_of_data` - The end of the input audio stream.
-   `reset` - The amount of audio that is currently being processed exceeds the two-minute maximum. The service splits the transcript to avoid excessive memory use.

### Split transcript at phrase end example
{: #split_transcript_example}

The following example requests demonstrate the use of the `split_transcript_at_phrase_end` parameter. The audio speaks the phrase "I have an identification card. The number is one two three four five six." The speaker pauses for one second between the words "three" and "four."

The first example shows the results for a request that omits the parameter. The service returns two final results, splitting the transcript only at the speaker's extended pause.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/wav" \
--data-binary @{path}audio-file.wav \
"{url}/v1/recognize"
```
{: pre}

```javascript
{
  "results": [
    {
      "alternatives": [
        {
          "confidence": 0.93,
          "transcript": "I have a valid identification card the number is one two three "
        }
      ],
      "final": true
    },
    {
      "alternatives": [
        {
          "confidence": 0.99,
          "transcript": "four five six "
        }
      ],
      "final": true
    }
  ],
  "result_index": 0
}
```
{: codeblock}

The second example recognizes the same audio but sets `split_transcript_at_phrase_end` to `true`. The service returns three final results, adding a result for the semantic stop after the first sentence. Each result includes the `end_of_utterance` field to identify the reason for the split.

```bash
curl -X POST -u "apikey:{apikey}" \
--header "Content-Type: audio/wav" \
--data-binary @{path}audio-file.wav \
"{url}/v1/recognize?split_transcript_at_phrase_end=true"
```
{: pre}

```javascript
{
  "results": [
    {
      "alternatives": [
        {
          "confidence": 0.92,
          "transcript": "I have a valid identification card "
        }
      ],
      "final": true,
      "end_of_utterance": "full_stop"
    },
    {
      "alternatives": [
        {
          "confidence": 0.97,
          "transcript": "the number is one two three "
        }
      ],
      "final": true,
      "end_of_utterance": "silence"
    },
    {
      "alternatives": [
        {
          "confidence": 0.99,
          "transcript": "four five six "
        }
      ],
      "final": true,
      "end_of_utterance": "end_of_data"
    }
  ],
  "result_index": 0
}
```
{: codeblock}
