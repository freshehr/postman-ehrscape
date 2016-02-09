# Getting ready
This section describes a number of steps to get ready for working with Ehrscape.

## Download files from email
You will have received an email containing the collection and environment files to use with Postman. The first step is to download these files ready to then be imported into Postman.

Find `NHS_Code4Health_Ehrscape.json.postman_collection` in your email and download it to a folder of your choice (normally the Download folder).

Find `C4H Ripple OSI.postman_environment` in your email and download it to a folder of your choice (normally the Download folder).

## Import files into Postman
Open Postman and select `Import`

![Import Files Into Postman](./Images/ImportFilesIntoPostman.jpg)

Locate your two save files and import them. You can either use the `Choose Files` option to import one at a time or drag and drop the files into the window

![Drop Files into Postman](./Images/DropFilesInPostman.jpg)

In the top right hand corner, change the environment to your environment (in the screenshot below that's the C4H Ripple OSI environment)

![Change environment](./Images/SelectEnvironment.jpg)

On the left hand side you can now see the collection files

![Collection](./Images/Collection.jpg)

## Navigate Postman
Click on the `NHS_Code4Health_Ehrscape collection` to reveal the folders, and then click on individual folders to expand and reveal the contents

![Navigate Postman](./Images/NavigatePostman.jpg)

Selecting one of the API calls displays more detail on the right hand side

![Select Call](./Images/APICallDetail.jpg)

Clicking on the ``x`` icon in the top right hand corner reveals a number of pre-set variables for your selected environment (again the C4H Ripple environment is used just as an example)

![Presets](./Images/Presets.jpg)


# Working with Ehrscape
We are now ready to start working properly with Ehrscape.

## Create Session
The first step is to create an openEHR session and retrieve the ``sessionId`` token. This allows subsequent API calls to be made without needing to login on each occasion.

Navigate to the `session` folder and highlight `Create Session` on the left, then click on the `Send` button.

![Create Session](./Images/CreateSession.jpg)

Click the `Scroll to responses` button in the bottom right hand corner to display the response details.

The screenshot below shows the session Id which has been returned in the call.

![Session Id](./Images/CreateSessionResult.jpg)

## Get Patient EHR Identifier
The next step is to get the patient’s internal EHR identifier by sending their external identifier (in this case NHS Number). The ehrId is a unique string which, for security reasons, cannot be associated with the patient, if for instance their openEHR records were leaked.

Select `Get ehrStatus from subjectId` in the `ehr` folder and then click on the `Send` button.

![Get EhrId](./Images/GetEhrId.jpg)

Click on the `Scroll to responses` button in the bottom right hand corner to display the response details.

The JSON snippet below shows the ehrId for our dummy patient.

![Display EhrId](./Images/GetEhrIdResult.jpg)

To store the returned ehrId as a pre-set for the selected environment, highlight the string in the response details, right mouse click, set the environment (*C4H Ripple OSI in this example*) and then select `ehrId` from the list of attributes.

![Store ehrId](./Images/StoreEhrIDAsPreset.jpg)

## Retrieve a composition Id
Now that we have the patient’s EHR identifier, we can use it to locate and retrieve some clinical details. We use an Archetype Query Language (AQL) call to retrieve a list of the identifiers and dates of existing Nursing Vital Signs Observations Composition records. Compositions are document-level records which act as the container for all openEHR patient data.

The name/value of the Composition is the root name of the templates composition archetype (case-sensitive). In a real-world example we would query on other factors to ensure we had the ‘correct’ list.

The query we need to run in order to get the composition Id for the most recent vital signs composition for the selected patient is as follows:

![Retrieve Composition Id Explanation](./Images/RetrieveCompositionIdExplanation.jpg)

Open the `query` folder and select `Ad-hoc query`

![Retrieve Composition Id](./Images/RetrieveCompositionIdFolderSelect.jpg)

This is the query string in a format which can be copied and pasted:
```
select
a/uid/value as compositionId,
a/context/start_time/value as start_time
from EHR e[ehr_id/value='{{ehrId}}']
contains COMPOSITION a[openEHR-EHR-COMPOSITION.encounter.v1]
where a/name/value= 'Nursing Vital Signs Observations'
order by a/context/start_time/value desc
offset 0 limit 1
```
In the Ad-hoc Query window click on `Param` and paste the query string above into the `Value` field, then click on the `Send` button.

![Get Composition Id](./Images/RetrieveCompositionIdString.jpg)

Click the `Scroll to response` button in the buttom right hand corner to display the response details. The `compositionId` element in the response is the unqique identifier for the composition and the `start_time` is the time that the document was authored.

![Get Composition Id result](./Images/RetrieveCompositionIdResult.jpg)

We will use the results of this query to retrieve the full composition, so the final action is to store the composition Id as a preset.

Highlight the string in the response details, right mouse click, set the environment (`C4H Ripple OSI` in this example) and then select `compositionId` from the list of attributes

![Store composition Id](./Images/StoreCompositionIdAsPreset.jpg)

## Retrieve full composition based on compositionId
The next step is to retrieve the composition itself, based on the compositionId we stored in the previous step.

Navigate to the `composition` folder and highlight `Read Composition JSON FLAT`, then click the `Send` button

![Retrieve Composition](./Images/RetrieveComposition.jpg)

The result is shown as a FLAT JSON file below

![Retrieve composition result](./Images/RetrieveCompositionResult.jpg)

Other formats are JSON RAW, XML RAW or JSON STRUCTURED – the snippets below show part of the Pulse data

![JSON RAW](./Images/RetrieveCompositionResultOtherFormats1.jpg)

![XML RAW](./Images/RetrieveCompositionResultOtherFormats2.jpg)

![JSON STRUCTURED](./Images/RetrieveCompositionResultOtherFormats3.jpg)

## Persist composition
The next step is to persist a new composition. The data in the composition is validated against a template, and the first action is to set the correct template Id for composition to be persisted.

Navigate to the `Template` folder and highlight `List available templates`, then click the `Send` button. Highlight the `Vital Signs Encounter template` in the list of available templates, right mouse click, set the environment (`C4H Ripple OSI` in this example) and then select `templateId` from the list of attributes

![Set templateId](./Images/ListTemplates.jpg)

Once the template Id is set, we can commit a composition. The following string is an example of a vital signs composition:
```
{
 "ctx/language": "en",
 "ctx/territory": "GB",
 "ctx/composer_name": "Hazel Smith",
 "ctx/time": "2015-12-10T02:19:00.000Z",
 "ctx/health_care_facility|id": "999999-345",
 "ctx/health_care_facility|name": "Northumbria Community NHS",
 "ctx/id_namespace": "NHS-UK",
 "ctx/id_scheme": "2.16.840.1.113883.2.1.4.3",
    "nursing_vital_signs_observations/vital_signs:0/respirations:0/any_event:0/rate|magnitude": 22,
    "nursing_vital_signs_observations/vital_signs:0/respirations:0/any_event:0/rate|unit": "/min",
    "nursing_vital_signs_observations/vital_signs:0/pulse:0/any_event:0/heart_rate|magnitude": 101,
    "nursing_vital_signs_observations/vital_signs:0/pulse:0/any_event:0/heart_rate|unit": "/min",
    "nursing_vital_signs_observations/vital_signs:0/body_temperature:0/any_event:0/temperature|magnitude": 36.6,
    "nursing_vital_signs_observations/vital_signs:0/body_temperature:0/any_event:0/temperature|unit": "°C",
    "nursing_vital_signs_observations/vital_signs:0/blood_pressure:0/any_event:0/systolic|magnitude": 100,
    "nursing_vital_signs_observations/vital_signs:0/blood_pressure:0/any_event:0/systolic|unit": "mm[Hg]",
    "nursing_vital_signs_observations/vital_signs:0/blood_pressure:0/any_event:0/diastolic|magnitude": 60,
    "nursing_vital_signs_observations/vital_signs:0/blood_pressure:0/any_event:0/diastolic|unit": "mm[Hg]",
    "nursing_vital_signs_observations/vital_signs:0/indirect_oximetry:0/any_event:0/spo2|numerator": 94,
    "nursing_vital_signs_observations/vital_signs:0/indirect_oximetry:0/any_event:0/spo2|denominator": 100,
    "nursing_vital_signs_observations/vital_signs:0/national_early_warning_score_rcp_uk:0/total_score": 3
  }
  ```
  
