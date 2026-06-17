# Respondent Management System to EQ Runner: Payload Version 2

This document defines the JWT payload structure for v2. This often referred to as the launch claims, launch token, or launch JWT.

**Prerequisites:**

- All non JWT specific date time properties are expressed using ISO 8601 and are assumed to be normalised to UTC unless a timezone identifier is given.
- All character encoding is UTF-8

## Schema Definition

### Required Fields

The following metadata properties are always required for the EQ Runner, they do not appear in individual survey metadata definitions.

| **Property**                | **Definition**                                                                                                                                 |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------|
| **iat**                     | JWT Issued At claim, see https://tools.ietf.org/html/rfc7519#section-4.1.6                                                                     |
| **exp**                     | JWT Expiration Time claim, see https://tools.ietf.org/html/rfc7519#section-4.1.4                                                               |
| **jti**                     | See [JWT Profile][jwt_profile]                                                                                                                 |
| **account_service_url**     | The base URL of the calling service used to launch the survey                                                                                  |
| **case_id**                 | The case UUID, used to identify a single instance of a survey collection for a respondent                                                      |
| **collection_exercise_sid** | A reference UUID used to represent the collection exercise inside the ONS                                                                      |
| **response_id**             | A unique identifier for the questionnaire response, this is used when saving and resuming a partially completed EQ across multiple sessions.   |
| **tx_id**                   | See: [JWT Profile][jwt_profile]                                                                                                                |
| **version**                 | The version number for this JWT payload specification. For this format, this must be `v2`.                                                     |
| **response_expires_at**     | An ISO_8601 formatted date-time after which the unsubmitted partial response can be deleted from the database                                  |

#### Schema Selection Fields

The schema selection field determine the mechanism used by EQ Runner to load the questionnaire schema JSON.

The schema used by an EQ Runner can be selected one of three ways.

| **Property**           | **Definition**                                                                                                      |
|------------------------|---------------------------------------------------------------------------------------------------------------------|
| **schema_url**         | A URL for a remote survey JSON. This claim is used to tell EQ Runner to load the schema JSON from a remote location |
| **schema_name**        | The name of the schema to launch. Must be present in [Schemas Repo][schemas_repo]                                   |
| cir_instrument_id      | **deprecated**: The UUID of the collection instrument to launch from the Collection Instrument Registry             |
| **TBA:** Census 2027   | A combination of attributes allowing EQ Runner to resolve to the predefined schema naming convention for Census     |  

### Optional Fields

EQ Runner can optionally accept the following keys.

| **Property**            | **Definition**                                                                                                                                                                                                                                             |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **channel**             | The channel (client) from which the questionnaire was launched                                                                                                                                                                                             |
| **language_code**       | Language code identifier, used to change language displayed. Format as per ISO-639-1 (https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) e.g. "en" for English; "cy" for Welsh. This parameter is currently optional; the default is "en"              |
| **region_code**         | The Region Code of the questionnaire response. Format as per ISO 3166-2 (https://en.wikipedia.org/wiki/ISO_3166-2:GB) i.e. `GB-ENG` / `GB-WLS` / `GB-NIR`. This is used in tactical legacy solutions for Individual Response, Email and Feedback features. |
| **survey_metadata**     | See: [Survey Metadata Fields][survey_metadata_fields]                                                                                                                                                                                                      |

### Survey Metadata Fields

In addition to the above [Required Runner Fields][required_runner_fields], some surveys require other data to be passed to EQ Runner for use within a questionnaire or for it to be sent downstream for receipting purposes. These should be passed via the `survey_metadata` property in the JWT payload.

| **Property**        | **Child Property**   | **Definition**                                                                                                                                                                                   |
| ------------------- |----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **survey_metadata** | JSON Object `{...}` | A mandatory n object to hold data about the survey and any additional keys required for receipting.                                                                                              |
|                     | **receipting_keys**  | An optional array of key names from the `survey_metadata.data` spec below that are required for downstream processing. The key names defined here must exist in `survey_metadata.data` property. |
|                     | **data**             | See: [Data Property][survey_metadata_data_property]                                                                                                                                              |

#### Data Property

The `survey_metadata.data` property contains key-value pairs of data about the survey. This data may contain a mixture of survey specific and respondent specific data.
For example, it may contain data common to all respondents for a given survey and data specific to the respondent filling in the survey.
The key values required within this object dependent upon two things:

1. The optional `survey_metadata.receipting_keys` defined in the JWT payload. EQ Runner will validate that keys specified in this field exists within `survey_metadata.data`.
2. The mandatory `metadata` defined in the schema JSON. These are commonly used for piping (rendering) / routing, but can also be used to require additional data in the payload that are sent downstream.
   1. The author of the schema JSON is responsible for marking metadata keys as required and to differentiate between different survey types.
   2. EQ Runner will validate that keys specified in the schema metadata exists within the `survey_metadata.data` field and that it matches the type specified in the JSON schema.

The data property must adhere to the [Census Survey Metadata][census_survey_metadata] specification.

##### Census Survey Metadata

| **Property**         | **Definition**                                                                                                     |
|----------------------|--------------------------------------------------------------------------------------------------------------------|
| **case_type**        | The type of case (e.g. "HH", "HI", "CE" or "SPG")                                                                  |
| **form_type**        | The particular predefined `form_type` for the case (e.g. "H", "I" or "C")                                          |
| **display_address**  | A mandatory string containing the case's address to be displayed                                                   |
| **period_id**        | A mandatory string representing the recognised time period for the collection exercise (e.g. "2027" or "2031")     |
| **ru_ref**           | The reporting unit reference, for example a case's UPRN or other address identifier                                |
| **user_id**          | An mandatory id assigned by the respondent management system, for example representing a Contact Centre operative |
| **questionnaire_id** | The questionnaire id for the case                                                                                  |    

For a list of required fields please view [survey metadata definition schema](../schemas/common/survey_metadata.json#L53).
An example of a valid schema can be found in examples, payload_v2, [launch_jwt_census](../examples/rm_to_eq_runner/payload_v2/launch_jwt_census.json)

## An example JSON claim for a Census survey

```json
{
    "account_service_log_out_url": "http://localhost:8000/logout",
    "account_service_url": "http://localhost:8000",
    "case_id": "823aef08-55ec-4211-a9e4-c99036d4d119",
    "channel": "rh",
    "collection_exercise_sid": "5a8a75e4-dd9f-4fd5-b802-e53e9e593f85",
    "eq_id": "census",
    "exp": 1781699607,
    "iat": 1781692407,
    "jti": "a7543507-3eae-4a3b-867a-91459540b1b6",
    "language_code": "en",
    "region_code": "GB-ENG",
    "response_expires_at": "2027-06-22T13:23:39+00:00",
    "response_id": "1929",
    "schema_name": "census_household_gb_eng",
    "survey": "CENSUS",
    "survey_metadata": {
        "data": {
            "case_type": "HH",
            "display_address": "123 Credibility Street, Newtown, NT108XG",
            "form_type": "H",
            "period_id": "2027",
            "questionnaire_id": "1234567890",
            "ru_ref": "uprn:00001",
            "user_id": "UNKNOWN"
        }
    },
    "tx_id": "1030d361-4226-4d78-af75-9607e1331896",
    "version": "v2"
}
```

[jwt_profile]: jwt_profile.md "JWT Profile Definition"
[schemas_repo]: https://github.com/ONSdigital/census31-eq-questionnaire-schemas/tree/main/schemas "Schemas Repo"
[required_runner_fields]: #required-fields "Required Fields"
[survey_metadata_fields]: #survey-metadata-fields "Survey Metadata Fields"
[survey_metadata_data_property]: #data-property "Survey Metadata Data Property Definition"
[census_survey_metadata]: #census-survey-metadata "Census Survey Metadata"
