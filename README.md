# SEQu-HEALTH Pro Webhook Documentation

_Currently in Development_

Whenever an employee completes their Daily Health Declaration and Temperature Check on SEQu-HEALTH Pro, you can be alerted immediately and process the data on your systems using API Webhooks.

## Setup

Your webhook url will be configurable in the SEQu-SUITE Admin Panel.

Kindly make sure your url is a `POST` endpoint.

## Security

To ensure your security, we add a `SEQU-SUITE-Signature` HTTP header to the data that we send to your webhook. This header can be used to verify that the request came from SEQu-SUITE's servers.

### Using the Signature

When we activate your account, we will send you a secret that we use to create the `SEQU-SUITE-Signature`

1. Create your own signature: Run the `body` of the JSON payload we sent you through HMAC with SHA256 hash function, using the secret we sent you during account activation as the signing key.
2. Compare your signature with the `SEQU-SUITE-Signature`. If the values match, then the request is valid as it came from us.

---

## Request Body Parameters

#### `timestamp`

UTC ISO 8601 Date and Time when employee completed the Health Declaration.

#### `healthAlert`

Boolean. `true` when employee has temperature above 37.5 degrees Celsium, declared symptoms, and/or declared possible exposure to COVID-19.

#### `personData`

Object containing employee's personal info.

#### `symptomData`

Object containing answers to employee's health declaration.

#### `addtlQuestions`

Array containing answers to additional custom questions set by your company.

---

### `personData` Parameters

The following data is collected:
- Name
- ID Number
- Department
- Residence
- Age
- Sex

---

### `symptomData` Parameters

#### `soreThroat`

YES/NO answer to "Are you experiencing sore throat?"

#### `bodyPains`

YES/NO answer to "Are you experiencing body pains?"

#### `headache`

YES/NO answer to "Are you experiencing headache?"

#### `fever`

YES/NO answer to "Are you experiencing fever for the past few days?"

#### `covidContact`

YES/NO answer to "Have you worked together or stayed in the same close environment of a confirmed COVID-19 case?"

#### `suspectContact`

YES/NO answer to "Have you had any contact with anyone with fever, cough, colds, and sore throat in the past 2 weeks?"

#### `travelAbroad`

YES/NO answer to "Have you travelled outside of the Philippines in the past 14 days?"

#### `travelCity`

Answer to "Have you travelled to any area in `[region]` aside from your home?" Where `region` is the administrative area your company is located (eg. Metro Manila, Metro Cebu)

If the employee's answer is "NO", this value will return `"NO"`

If the employee's answer is "YES", they will be asked to specify where in region they have travelled, which will be the value of the `travelCity` property.

#### `triage`

Headline recommendation by our AI symptom checkup. If the employee did not need to undergo the checkup, this value will be an empty string (`""`).

#### `triageDecs`

Detailed information about the recommendation made by our AI symptom checkup. If the employee did not need to undergo the checkup, this value will be an empty string (`""`).

## Example Request Body

```
{
    "timestamp": "2020-06-14T12:32:57.930Z",
    "healthAlert": true,
    "temperature": 35.74,
    "personData": {
        "age": "20",
        "cellphone": "09171234567",
        "department": "Engineering",
        "idNumber": "123456",
        "name": "Julian Patdu",
        "residence": "221B Baker Street",
        "sex": "MALE"
    },
    "symptomData": {
        "bodyPains": "YES",
        "covidContact": "NO",
        "fever": "NO",
        "headache": "YES",
        "soreThroat": "NO",
        "suspectContact": "NO",
        "travelAbroad": "NO",
        "travelCity": "NO",
        "triage": "Stay home and monitor your symptoms.",
        "triageDesc": "Your symptoms do not suggest that you have the COVID-19. Still, it is better if you recover from your illness or symptoms at home for your and other people’s safety. Remember that your symptoms may also result from other diseases and may require medical consultation - this interview targets the COVID-19 infection. If your symptoms seem severe and you are worried, contact your doctor or local health authorities."
    },
    "addtQuestions": [
        {
            "a": "alpha echo ",
            "q": "Pick your favorite letters of the alphabet. This is a custom multi-select question."
        },
        {
            "a": 42,
            "q": "What is the meaning of life? This is a custom single-select question."
        },
        {
            "a": "SKIPPED",
            "q": "This is a sample textbox question that has been skipped."
        }
    ]
}
```

## Testing

We will be providing an interface in the admin panel to test-fire the webhook with dummy data.