# Launch Payload (Version 2)

This document defines the JWT claims to be used when launching a questionnaire in EQ. The claims are used to authenticate a user, select a questionnaire schema, and pass data used in the questionnaire or to be submitted downstream.

**Prerequisites:**

- All non JWT specific date time properties are expressed using ISO 8601 and are assumed to be normalised to UTC unless a timezone identifier is given.
- All character encoding is UTF-8

All properties are mandatory unless marked optional.

| **Property**                | **Definition**                                                                                                                                                                                                         |
| --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **jti**                     | See [JWT Profile][jwt_profile]                                                                                                                                                                                         |
| **tx_id**                   | See [JWT Profile][jwt_profile]                                                                                                                                                                                         |
| **iat**                     | JWT Issued At claim, see [RFC 7519 section 4.1.6](https://tools.ietf.org/html/rfc7519#section-4.1.6)                                                                                                                   |
| **exp**                     | JWT Expiration Time claim, see [RFC 7519 section 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4)                                                                                                             |
| **version**                 | The version number for this JWT payload specification. This must be `v2`.                                                                                                                                              |
| **channel**                 | The channel (client) from which the questionnaire was launched e.g. `rh`, `cc` or `ad`                                                                                                                                 |
| **case_id**                 | The case UUID, used to identify a single instance of a survey collection for a respondent                                                                                                                              |
| **collection_exercise_sid** | A reference UUID used to represent the collection exercise inside the ONS                                                                                                                                              |
| **response_id**             | A unique identifier for the questionnaire response. This enables saving and resuming a partially completed questionnaire across multiple sessions if the same identifier is provided.                                  |
| **account_service_url**     | The base URL of the calling service used to launch the survey                                                                                                                                                          |
| **schema**                  | A JSON object containing properties EQ resolves to the questionnaire schema to launch. See [Schema Selection Properties][schema_selection_properties].                                                                 |
| **survey_metadata**         | A JSON object for data used within a questionnaire or to be sent downstream with the submission. See [Survey Metadata][survey_metadata].                                                                               |
| **language_code**           | _(optional)_ Language code identifier, used to change language displayed. Format as per [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) e.g. `en` for English; `cy` for Welsh. The default is `en`. |

## Schema Selection Properties

The top-level `schema` property contains three properties that EQ resolves to the questionnaire schema to launch. All three are mandatory:

| **Property**    | **Definition**                                                                                                                                                |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **survey**      | The type of survey e.g. `CENSUS`, `CCS`.                                                                                                                      |
| **form_type**   | The form type for the case e.g. `H`, `I` or `C`. For questionnaire variants this could be `HA`, `HB`, `IA` or `IB`.                                           |
| **region_code** | The region code of the questionnaire. Format as per [ISO 3166-2](https://en.wikipedia.org/wiki/ISO_3166-2:GB) i.e. `GB-ENG` / `GB-WLS` / `GB-NIR` / `GB-SCT`. |

## Survey Metadata

The top-level `survey_metadata` property is for data used within a questionnaire or to be sent downstream with the submission. These properties are commonly used for rendering placeholders and routing, but can also be properties that are sent downstream. All properties are mandatory unless marked optional:

| **Property**         | **Definition**                                                                                                        |
| -------------------- | --------------------------------------------------------------------------------------------------------------------- |
| **case_type**        | The type of case e.g. `HH`, `HI`, `CE` or `SPG`                                                                       |
| **display_address**  | The address displayed to the user in the launched survey e.g. `15 Credibility Street, Bristol, BS7 8ES`               |
| **ru_ref**           | The reporting unit reference, for example a case's UPRN or other address identifier e.g. `uprn:00001`                 |
| **questionnaire_id** | The questionnaire id for the case                                                                                     |
| **user_id**          | _(optional)_ The ID assigned by the respondent management system, for example representing a Contact Centre operative |

See the [survey metadata schema](../schemas/common/survey_metadata.json#L53) for more detail.

## Example JWT Payload

```json
{
  "jti": "a7543507-3eae-4a3b-867a-91459540b1b6",
  "tx_id": "7e916adc-1f36-4f1e-81f9-57a4ab4ad183",
  "iat": 1781692407,
  "exp": 1781699607,
  "version": "v2",
  "channel": "rh",
  "case_id": "f48e8790-f591-4086-9f6d-98c8642d96cb",
  "collection_exercise_sid": "77d00da0-9245-40a3-9219-a9f860066167",
  "response_id": "1929",
  "account_service_url": "http://localhost:8000",
  "schema": {
    "survey": "CENSUS",
    "form_type": "H",
    "region_code": "GB-ENG"
  },
  "survey_metadata": {
    "case_type": "HH",
    "display_address": "15 Credibility Street, Bristol, BS7 8ES",
    "questionnaire_id": "1234567890",
    "ru_ref": "uprn:00001"
  },
  "language_code": "en"
}
```

This example JSON can be found at [launch_jwt_v2.json](../examples/launch/payload_v2/launch_jwt_v2.json).

[jwt_profile]: jwt_profile.md "JWT Profile Definition"
[survey_metadata]: #survey-metadata "Survey Metadata"
[schema_selection_properties]: #schema-selection-properties "Schema Selection Properties"
