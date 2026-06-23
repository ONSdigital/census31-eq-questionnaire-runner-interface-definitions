# EQ Runner to Downstream: Payload Version 2

This document defines the downstream payload structure for version v2.

## Schema Definition

### Required Fields

| **Property**                 | **Definition**                                                                                                                                                                                                                                                                                                              |
|------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **tx_id**                    | Transaction ID used to trace a transaction through the collection system. This will be a UUID (version 4) and 128-bits in length as defined in RFC 4122 in its textual representation as defined in section 3 "Namespace Registration Template" without the "urn:uuid:" prefix e.g. "f81d4fae-7dec-11d0-a765-00a0c91e6bf6". |
| **type**                     | The unique type identifier of the JSON object. Either `uk.gov.ons.edc.eq:surveyresponse` or `uk.gov.ons.edc.eq:feedback`                                                                                                                                                                                                            |
| **version**                  | The version number of the payload specification. The format described in this document and used for Census EQ Runner is `v2`.                                                                                                                                                                                                                                      |
| **data_version**             | The version number of the questionnaire schemas `data_version`. Census EQ Runner uses `0.0.3`                                                                                                                                                                                                                               |
| **origin**                   | The name or identifier of the data capture system. Currently "uk.gov.ons.edc.eq" (historically named for Electronic Data Collection)                                                                                                                                                                                        |
| **collection_exercise_sid**  | A reference UUID used to represent the collection exercise inside the ONS                                                                                                                                                                                                                                                   |
| **case_id**                  | The case UUID used to identify an instance of a survey response request (generated in RM, may not be included if no case has been linked at launch time)                                                                                                                                                                    |
| **submitted_at**             | The datetime of the submitted survey response or feedback submitted by the respondent                                                                                                                                                                                                                                       |
| **launch_language_code**     | The language code that the questionnaire was launched with (e.g. `en` / `cy` / `ga` / `eo`)                                                                                                                                                                                                                                 |
| **submission_language_code** | The language code that was being used on submission (e.g. `en` / `cy` / `ga` / `eo`). This will not exist if `flushed` is `true`                                                                                                                                                                                            |
| **flushed**                  | Whether the `surveyresponse`or `feedback` was flushed or not. This will be `true` if the `surveyresponse` has been flushed and `false` otherwise. Feedback cannot be flushed and will always be `false`.                                                                                                                             |
| **survey_metadata**          | An object that holds metadata about the survey. For allowed values, See: [Submission Survey Metadata][submission_survey_metadata].                                                                                                                                                                                          |
| **data**                     | The response data. See: [EQ Runner Data Versions][eq_runner_data_versions]                                                                                                                                                                                                                                                  |

#### Submission Survey Metadata

- The submission `survey_metadata` will contain all key values from the `survey_metadata.data` property from the launch token. For allowed values, See: [Survey Metadata: Data Property][survey_metadata_data_property].
- Additionally, the `survey_metadata.survey_id` property will be included, the value of this is assigned in the questionnare schema and is an ONS wide identifier for the survey (e.g. `census` for the Census, `009` for the Monthly Business Survey).

#### Schema Selection Fields

In additional to the field above, a schema field will be provided which defines the mechanism that was used by EQ Runner to load the questionnaire schema JSON.

One of the following will be present:

| **Property**          | **Definition**                                                                                                                                                                                                                                                                                                      |
|-----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **schema_url**        | The URL to the remote survey JSON.                                                                                                                                                                                                                                                                                  |
| **schema_name**       | The name of the schema launched. Will be present in [Schemas Repo][schemas_repo]                                                                                                                                                                                                                                    |

### Optional Fields

EQ Runner will pass the following keys if a value for them exists.

| **Property**        | **Definition**                                                                                                                     |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| **region_code**     | The Region Code of the questionnaire response. Format as per ISO 3166-2 () e.g. `GB-ENG` / `GB-WLS` / `GB-NIR`                     |
| **started_at**      | The datetime of the first form submission in the questionnaire.                                                                    |
| **channel**         | The channel used to launch the electronic questionnaire                                                                            |

## Example JSON payload

```json
{
  "case_id": "f48e8790-f591-4086-9f6d-98c8642d96cb",
  "tx_id": "7e916adc-1f36-4f1e-81f9-57a4ab4ad183",
  "type": "uk.gov.ons.edc.eq:surveyresponse",
  "version": "v2",
  "data_version": "0.0.3",
  "origin": "uk.gov.ons.edc.eq",
  "collection_exercise_sid": "77d00da0-9245-40a3-9219-a9f860066167",
  "flushed": false,
  "submitted_at": "2026-06-19T14:06:27+00:00",
  "launch_language_code": "en",
  "survey_metadata": {
    "survey_id": "census",
    "user_id": "UNKNOWN",
    "period_id": "2027",
    "display_address": "15 Credibility Street, Bristol, BS7 8ES",
    "form_type": "H",
    "questionnaire_id": "1234567890",
    "case_type": "HH",
    "ru_ref": "uprn:00001"
  },
  "schema_name": "census_household_gb_eng",
  "data": {
    "answers": [...],
    "lists": [
      {
        "items": ["tjwCXg", "obJDFL", "EsZBbD", "THaczE"],
        "name": "household",
        "primary_person": "tjwCXg"
      }
    ]
  },
  "channel": "rh",
  "region_code": "GB-ENG",
  "started_at": "2026-06-19T14:04:53.323989+00:00",
  "submission_language_code": "en"
}

```

For additional `data` version examples, see [EQ Runner Data Versions][eq_runner_data_versions]

[eq_runner_data_versions]: eq_runner_data_versions.md "EQ Runner Data Versions"
[schemas_repo]: https://github.com/ONSdigital/census31-eq-questionnaire-schemas/tree/main/schemas "Schemas Repo"
[survey_metadata_data_property]: rm_to_eq_runner_payload_v2.md#data-property "Survey Metadata: Data Property"
[submission_survey_metadata]: #submission-survey-metadata "Submission Survey Metadata"
