# EQ Runner to Downstream Ingestion Service

All submitted questionnaire responses and feedback are transformed into data formats described below for downstream processing and analysis. The format used is specified by the `version` claim provided in the launch JWT token. Currently, only [Payload Version 2](eq_runner_to_downstream_payload_v2.md) is supported.

The data structures included in the payloads are designed and optimised for the purposes of generic functionality within the EQ Runner application. It is not the responsibility of EQ Runner to carry out any transforms on submitted response data beyond its native data models, nor on any claims provided by the launching system and included in the response data. Any transforms will be carried out by downstream systems as required.

The response JSON is encrypted using the public key of the downstream transport mechanism at submission and signed by the EQ Runner private key for downstream verification.

## GCS Submitter

When the EQ Runner GCS submitter is configured, an object containing the response ciphertext is written to Cloud Storage for downstream consumption.

- For `surveyresponse` objects, the object ID is named for the response's `tx_id`.
- For `feedback` objects, the object ID is named with a uniquely generated UUID.

The GCS response object contains associated [metadata][gcs_metadata] which can be used in a Pub/Sub messaging strategy for further event driven processes (e.g. receipting and triggering ingestion flow).
Metadata will always contain a `tx_id` and a `case_id`.
Additional receipting metadata may be added, which are defined by `survey_metadata.receipting_keys` from the JTW launch token.

### Example v2 GCS metadata

```json
{
	"tx_id": "6fcf3ddc-a685-4aa1-8fcf-3e38aed5cbf7",
	"case_id": "2859a8b5-34c3-4603-aad9-78198d8341c9",
	"qid": "bdf7dff2-1d73-4b97-bd2d-91f2e53160b9"
}
```

## Low-level data types

- All datetimes are expressed using ISO_8601 and are assumed to be normalised to UTC unless a timezone identifier is given.
- All character encoding is UTF-8.
- All boolean responses are matched to a "True" or "False" string representation.
- Unanswered optional questions are not included in submitted responses (i.e. null or empty strings values are NOT included)

## Payload Formats

The downstream payload format is set using the `version` that was provided in the launch token, currently only [Payload Version 2](eq_runner_to_downstream_payload_v2.md) is supported. This is not to be confused with runner's `data_version` which is responsible for the structure of the `data` property within the payload.

## JWT envelope / transport

This payload is part of a JWT, as specified in [JWT Profile][jwt_profile].

[gcs_metadata]: https://cloud.google.com/storage/docs/viewing-editing-metadata "GCS Metadata"
[jwt_profile]: jwt_profile.md "JWT Profile Definition"
[eq_runner_to_downstream_payload_v2]: eq_runner_to_downstream_payload_v2.md "EQ to Downstream Runner Payload v2 Definition"
[rm_to_eq_runner]: respondent_management_to_electronic_questionnaire_runner.md "RM to EQ Runner"
[payload_formats]: electronic_questionnaire_runner_to_downstream#payload-formats "Payload Formats"
