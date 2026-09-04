# Submission Payload (Version 2)

The payload `version` is currently fixed to `v2`. This is not to be confused with `data_version` which determines the structure of the `data` property within the payload.

Low-level data types:

- All datetimes are expressed using ISO_8601 and are assumed to be normalised to UTC unless a timezone identifier is given.
- All character encoding is UTF-8.
- All boolean responses are matched to a "True" or "False" string representation.
- Unanswered optional questions are not included in submitted responses (i.e. null or empty strings values are NOT included)

| **Property**                 | **Definition**                                                                                                                                                                                                                                                                                                              |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **version**                  | The version number of the submission payload. The format described in this document and used for Census EQ is `v2`.                                                                                                                                                                                                         |
| **origin**                   | The name or identifier of the data capture system. Currently `uk.gov.ons.edc.eq` (historically named for Electronic Data Collection)                                                                                                                                                                                        |
| **type**                     | The unique type identifier of the JSON object. Either `uk.gov.ons.edc.eq:surveyresponse` or `uk.gov.ons.edc.eq:feedback`                                                                                                                                                                                                    |
| **tx_id**                    | Transaction ID used to trace a transaction through the collection system. This will be a UUID (version 4) and 128-bits in length as defined in RFC 4122 in its textual representation as defined in section 3 "Namespace Registration Template" without the "urn:uuid:" prefix e.g. `f81d4fae-7dec-11d0-a765-00a0c91e6bf6`. |
| **collection_exercise_sid**  | A reference UUID used to represent the collection exercise inside the ONS                                                                                                                                                                                                                                                   |
| **case_id**                  | The case UUID used to identify an instance of a survey response request (generated in RM, may not be included if no case has been linked at launch time)                                                                                                                                                                    |
| **channel**                  | The channel used to launch the electronic questionnaire: `rh` (respondent home), `cc` (contact centre) or `ad` (assisted digital).                                                                                                                                                                                          |
| **period_id**                | A string representing the recognised time period for the collection exercise e.g. `2027` or `2031`                                                                                                                                                                                                                          |
| **schema**                   | An object containing properties EQ used to resolve the questionnaire schema name. The region code of the questionnaire response is in this object. See [Launch: Schema Selection Properties][schema_selection_properties].                                                                                                  |
| **schema_name**              | The schema name resolved from the `schema` object properties e.g. `census_household_gb_eng`. The schema JSON (`<schema_name>.json`) can be found in a [Census 2031 Schemas repo release][census_schemas_repo_releases] in the `schemas.zip` Assets download.                                                                |
| **survey_metadata**          | This will contain all properties from `survey_metadata` in the launch token. See [Launch: Survey Metadata Property][survey_metadata_property].                                                                                                                                                                              |
| **launch_language_code**     | The language code that the questionnaire was launched with e.g. `en` / `cy` / `ga` / `eo`                                                                                                                                                                                                                                   |
| **submission_language_code** | The language code that was in use on submission e.g. `en` / `cy` / `ga` / `eo`. This will not be present if `flushed` is `true`                                                                                                                                                                                             |
| **started_at**               | The datetime the user started their questionnaire. This is recorded at the first question submission in the questionnaire.                                                                                                                                                                                                  |
| **submitted_at**             | The datetime of the submitted survey response or feedback submitted by the respondent                                                                                                                                                                                                                                       |
| **flushed**                  | Whether the `surveyresponse`or `feedback` was flushed or not. This will be `true` if the `surveyresponse` has been flushed and `false` otherwise. Feedback cannot be flushed and will always be `false`.                                                                                                                    |
| **data_version**             | The schema version of the data payload in the `data` property. Currently `0.0.3`                                                                                                                                                                                                                                            |
| **data**                     | The submission data. See [Submission Data][submission_data].                                                                                                                                                                                                                                                                |

## Example Payload

```json
{
  "version": "v2",
  "origin": "uk.gov.ons.edc.eq",
  "type": "uk.gov.ons.edc.eq:surveyresponse",
  "tx_id": "7e916adc-1f36-4f1e-81f9-57a4ab4ad183",
  "collection_exercise_sid": "77d00da0-9245-40a3-9219-a9f860066167",
  "case_id": "f48e8790-f591-4086-9f6d-98c8642d96cb",
  "channel": "rh",
  "period_id": "2027",
  "schema": {
    "survey": "CENSUS",
    "form_type": "H",
    "region_code": "GB-ENG"
  },
  "schema_name": "census_household_gb_eng",
  "survey_metadata": {
    "user_id": "UNKNOWN",
    "display_address": "15 Credibility Street, Bristol, BS7 8ES",
    "questionnaire_id": "1234567890",
    "case_type": "HH",
    "ru_ref": "uprn:00001"
  },
  "launch_language_code": "en",
  "submission_language_code": "en",
  "started_at": "2026-06-19T14:04:53.323989+00:00",
  "submitted_at": "2026-06-19T14:06:27+00:00",
  "flushed": false,
  "data_version": "0.0.3",
  "data": {
    "answers": [...],
    "lists": [
      {
        "items": ["tjwCXg", "obJDFL", "EsZBbD", "THaczE"],
        "name": "household",
        "primary_person": "tjwCXg"
      }
    ]
  }
}
```

The full example submission JSON can be found at [surveyresponse_0_0_3.json](../examples/submission/payload_v2/surveyresponse_0_0_3.json).

An example feedback JSON can be found at [feedback_0_0_3.json](../examples/submission/payload_v2/feedback_0_0_3.json).

[survey_metadata_property]: launch_payload_v2.md#survey-metadata "Survey MetadataProperty"
[schema_selection_properties]: launch_payload_v2.md#schema-selection-properties "Schema Selection Properties"
[census_schemas_repo_releases]: https://github.com/ONSdigital/census31-eq-questionnaire-schemas/releases "Census 2031 Schemas repo release"
[submission_data]: submission_data.md "Submission Data"
