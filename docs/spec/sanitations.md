_Authors_: @SanduniU \
_Created_: 2024/08/05 \
_Updated_: 2024/08/07 \
_Edition_: Swan Lake

# Sanitation for OpenAPI specification

This document records the sanitation done on top of the official OpenAPI specification from OpenAI. The OpenAPI specification is obtained from the [OpenAI OpenAPI Documentation](https://github.com/openai/openai-openapi/blob/master/openapi.yaml). These changes are implemented to enhance the overall usability and readability of the generated client.

1. **Removed the `default:null` property of certain schemas**:

   - **Changed Schemas**: `CreateCompletionRequest`,`ChatCompletionStreamOptions`,`CreateChatCompletionRequest`

   - **Original**:
      - default: `null`

   - **Updated**:
      - Removed the `default` parameter 

   - **Reason**: This change is done as a temporary workaround until the Ballerina OpenAPI tool supports OpenAPI Specification version v3.1.x(Currently supported upto version 3.0.0)

2. **Removed the `status`, `incomplete_objects`, `completed_at`, `attachments` and `incomplete_at` attributes from the required fields of `MessageObject` schema**:

   - **Changed Schema**: `MessageObject`

   - **Original**:
      - Required fields: `status`, `incomplete_objects`, `completed_at`, `incomplete_at`, `attachments`

   - **Updated**:
      - Required fields: Removed `status`, `incomplete_objects`, `completed_at`,`attachments` and `incomplete_at`

   - **Reason**: This change addresses the error occurring when creating a new message due to the following missing required fields:

      ```plaintext
      error: Payload binding failed: 'map<json>' value cannot be converted to 'openai.Assistant:MessageObject': 
      missing required field 'completed_at' of type 'int?' in record 'openai.Assistant:MessageObject'
      missing required field 'incomplete_at' of type 'int?' in record 'openai.Assistant:MessageObject'
      missing required field 'incomplete_details' of type 'openai.Assistant:MessageObject_incomplete_details?' in record 'openai.Assistant:MessageObject'
      missing required field 'status' of type '("in_progress"|"incomplete"|"completed")' in record 'openai.Assistant:MessageObject'
      missing required field 'attributes' of type 'MessageObject_attachments[]?' in record 'openai.Assistant:MessageObject'
      ```

3. **ListRunStepsResponse schema: `first_id` and `last_id` attributes were made nullable (`nullable: true`)**:

   - **Changed Schema**: `ListRunStepsResponse`

   - **Original**:
      - `first_id`: `string`
      - `last_id`: `string`

   - **Updated**:
      - `first_id`: `string?`
      - `last_id`: `string?`

   - **Reason**: This change addresses the error occurring due to the following payload binding failure:

      ```plaintext
      Payload binding failed: 'map<json>' value cannot be converted to 'openai.Assistant:ListRunStepsResponse': 
      field 'first_id' in record 'openai.Assistant:ListRunStepsResponse' should be of type 'string', found '()'
      field 'last_id' in record 'openai.Assistant:ListRunStepsResponse' should be of type 'string', found '()'
      ```
4. **Removed the `stream` field from `CreateThreadAndRunRequest`, `CreateRunRequest`, and `SubmitToolOutputsRunRequest` records**:

   - **Changed Schemas**: `CreateThreadAndRunRequest`, `CreateRunRequest`, `SubmitToolOutputsRunRequest`

   - **Original**:
      - Included `stream` field for streaming responses(If `true`, returns a stream of events that happen during the Run as server-sent events, terminating when the Run enters a terminal state with a `data: [DONE]` message).

   - **Updated**:
      - Removed `stream` field.

   - **Reason**: This change is necessary because the Ballerina OpenAPI tool does not support the streaming responses feature, leading to payload binding failures.

5. **Removed the `expired_at` and `metadata` attributes from the required fields of `RunStepObject` schema**:

   - **Changed Schema**: `RunStepObject`

   - **Original**:
      - Required fields: `expired_at`, `metadata`

   - **Updated**:
      - Required fields: Removed `expired_at` and `metadata`

   - **Reason**: This change addresses the payload binding failures occurring due to the inclusion of `expired_at` and `metadata` as required fields.

6. **ListMessageObject schema: `first_id` and `last_id` attributes were made nullable (`nullable: true`)**:

   - **Changed Schema**: `ListMessageObject`

   - **Original**:
      - `first_id`: `string`
      - `last_id`: `string`

   - **Updated**:
      - `first_id`: `string?`
      - `last_id`: `string?`

   - **Reason**: This change is necessary to prevent binding errors that occur when there are no messages in the thread, as `first_id` and `last_id` need to be nullable in such cases.

---

## OpenAPI cli command

The following command was used to generate the Ballerina client from the OpenAPI specification. The command should be executed from the repository root directory.

```bash
bal openapi -i docs/spec/openapi.yaml --mode client --tags Assistants --license docs/license.txt -o ballerina
```
Note: The license year is hardcoded to 2024, change if necessary.
