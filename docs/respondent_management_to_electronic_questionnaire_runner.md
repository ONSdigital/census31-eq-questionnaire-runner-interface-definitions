# Respondent Management System to EQ Runner

A respondent will initially access the EQ Runner service from an upstream Respondent Management System (e.g. Respondent Home). This is typically by way of an `HTTP 302` browser redirect when the respondent "launches" a questionnaire from within the Response Management System. 
The redirect **MUST** include a set of well-defined attributes (claims) constructed by the Respondent Management System. These are required by EQ Runner to authenticate the respondent, and to provide the necessary data to render the required questionnaire.
This data is wrapped inside a JSON Web Token (JWT) passed in a `token` query string parameter in the redirected URL. The token value is digitally signed by a trusted client application (e.g. Respondent Home) to ensure the integrity and authenticity of the data being passed to EQ Runner.
This creates a clean interface for any Respondent Management System to integrate with the EQ Runner.

## JWT payload
The structure of the JWT payload that the Respondent Management System uses is be defined using the version property.
The downstream data is structured based on the version property provided in the claims, for information regarding downstream data formats, see [EQ Runner to Downstream Ingestion Service][eq_runner_to_downstream].

## JWT envelope / transport

This payload is part of a JWT, as specified in [JWT Profile][jwt_profile]. The encoded
JWT is appended to the URL of the receiving system as follows:

`https://<hostname>/session?token=<JWT>`

## Flushing responses

To flush responses to the downstream systems, a `/flush` endpoint is available.
This endpoint takes a JWT in the same way as `/session` but with `roles` including the role of `flusher`.

[jwt_profile]: jwt_profile.md "JWT Profile Definition"
[eq_runner_to_downstream]: electronic_questionnaire_runner_to_downstream.md "Electronic Questionnaire Runner to Downstream Definition"
[rm_to_eq_runner_payload_v2]: rm_to_eq_runner_payload_v2.md "RM to EQ Runner Payload v2 Definition"
