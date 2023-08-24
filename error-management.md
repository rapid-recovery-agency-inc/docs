# Error Management

Error management on the platform should be done using the following guidelines:

- All Business Services should throw exceptions when an error occurs.

- Business Services or service function should use the following error classes to raise exceptions:
    * `NotFoundError`: Generic error class for errors related to data not found.
    * `ValidationError`: Generic error class for errors related to data validation.
    * `PermissionError`: Generic error class for errors related to permissions or access to features.
    * `BusinessRuleError`: Generic error class for errors related to business rules.
    * `DataIntegrityError`: Generic error class for errors related to data integrity, specifically for when an operation
      on the database fails.
    * `Error`: Native error class for any other error type not described above.

- Controllers or handlers should manage errors using the express error middleware strategy: sending
  an `InsighttAPIError` class
  in the `next` function call. This error would be translated by the error middleware located
  in `/shared/middleware/error.ts` to a response and properly logged in AWS Cloudwatch.

## Example:

```typescript
import { ValidationError, InsighttAPIError } from '@errors';

const serviceFunction = async () => {
  if (someCondition) {
    throw new ValidationError('Some error message');
  }
  //
}

const controllerFunction = async (req, res, next) => {
  try {
    await serviceFunction();
    res.status(200).send('OK');
  } catch (error) {
    next(new InsighttAPIError({ operationName: 'serviceFunction', error }));
  }
}

```
