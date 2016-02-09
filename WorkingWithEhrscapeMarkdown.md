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

The screenshot below shows the session ID which has been returned in the call.
