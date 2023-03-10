# STEP 3: Create a voice-enabled COVID-19 chatbot using Node-RED

!!! warning 
    This material is part of the World Wide IBM Call for Code 2020 Solution Starter Kit.  Some links may not be working, however, the original contents are kept for reference only.

Learn how to create a voice-enabled chatbot using Node-RED and the IBM Watson Assistant, Watson Speech to Text, and Watson Text to Speech services.

## Authors

- [John Walicki](https://developer.ibm.com/profiles/walicki/) - IBM
- [Isaias Rafael Angeles](https://developer.ibm.com/profiles/isaias/) - IBM


## Learning objectives

In this tutorial, you will:

- Learn about Node-RED and see how to install it locally and on IBM Cloud
- Explore the node-red-node-watson Node-RED nodes
- Import and deploy the Watson Assistant chatbot example
- Build a Call for Code COVID Crisis Communications voice-enabled chatbot solution

## Prerequisites

- Register for an [IBM Cloud](https://cloud.ibm.com/registration) account, if you do not have an account yet. Otherwise, please proceed to IBM Cloud [login](https://cloud.ibm.com/login)
- Install Node-RED [locally](https://nodered.org/docs/getting-started/) or [Create a Node-RED Starter application](https://developer.ibm.com/tutorials/how-to-create-a-node-red-starter-application/) in IBM Cloud
    - After Node-RED is installed, add the dependencies:
    ```
    npm install node-red-node-ui-microphone node-red-dashboard node-red-node-watson node-red-contrib-play-audio
    ```
- [Create a Watson Assistant COVID-19 crisis communications chatbot](../assistant/README.md)

## Estimated time

Completing this tutorial should take about 30 minutes.

## Architecture diagram

### Voice enabled COVID Crisis Communications Chatbot using Node-RED

The following diagram shows the workflow for a Node-RED chatbot that answers questions about COVID-19.

![Crisis Comms Architecture diagram](../images/Crisis-Comms-Architecture-Node-RED.png)

1. A user visits a voice enabled Node-RED website with the COVID-19 chatbot and asks a question.
2. Node-RED records the speech wav audio format and calls the Watson Speech to Text service hosted in IBM Cloud.
3. Watson Speech to Text uses machine learning to decode the user's speech.
4. Watson Speech to Text replies with a transcript of the COVID-19 question and Node-RED calls Watson Assistant service hosted in IBM Cloud.
5. Watson Assistant uses natural language understanding and machine learning to extract entities and intents of the user question.
6. Source COVID-19 FAQ information from trusted CDC data.
7. Watson Assistant invokes an OpenWhisk open source-powered IBM Cloud Function.
8. IBM Cloud Function calls Watson Discovery service running in IBM Cloud.
9. Watson Discovery scans news articles and responds with relevant articles.
10. Watson Assistant invokes an OpenWhisk open source-powered IBM Cloud Function.
11. IBM Cloud Function calls COVID-19 API to get statistics.
12. Watson Assistant replies to the user inquiry and Node-RED sends the text transcript to Watson Text to Speech.
13. Watson Text to Speech encodes the message in the user's language.
14. Node-RED plays the chat answer .wav file to the user.
15. The user listens to the chat answer.

## Steps

### Learn about Node-RED

[Node-RED](http://nodered.org) is an open source programming tool for wiring together hardware devices, APIs, and online services in new and interesting ways. It provides a browser-based editor that makes it easy to wire together flows using the wide range of nodes in the palette that can be deployed to its runtime in a single-click.

### Install Node-RED Dependencies nodes

After Node-RED is installed, add the dependencies for this tutorial:

- [node-red-node-ui-microphone](https://flows.nodered.org/node/node-red-node-ui-microphone)
- [node-red-dashboard](https://flows.nodered.org/node/node-red-dashboard)
- [node-red-node-watson](https://flows.nodered.org/node/node-red-node-watson)
- [node-red-contrib-play-audio](https://flows.nodered.org/node/node-red-contrib-play-audio)

#### Local installation instructions

```
npm install node-red-node-ui-microphone node-red-dashboard node-red-node-watson node-red-contrib-play-audio
```

#### IBM Cloud installation instructions

- Instead of adding the additional packages via **Manage Palette**, use the IBM Cloud Toolchain and the git repository in IBM Cloud to add the following packages to the package.json. Commit the change and the CI/CD toolchain will restage the CF application.

```
"node-red-node-ui-microphone":"0.x",
"node-red-dashboard":"2.x",
"node-red-contrib-play-audio":"2.x",
```

### Explore node-red-node-watson Node-RED nodes

The [node-red-node-watson GitHub repository](https://github.com/watson-developer-cloud/node-red-node-watson) includes a collection of Node-RED nodes for IBM Watson services.  This package adds the following nodes to your Node-RED palette:

- Assistant: Add conversational capabilities into applications
- Discovery: List environments created for Watson Discovery
- Language Identification: Detect the language used in text
- Language Translator: Translate text from one language to another
- Natural Language Classifier: Use machine learning algorithms to return the top matching predefined classes for short text inputs
- Natural Language Understanding: Analyze text to extract meta-data from content such as concepts, entities, and keywords
- Personality Insights: Use linguistic analytics to infer cognitive and social characteristics from text
- Speech To Text: Convert audio containing speech to text
- Text To Speech: Convert text to audio speech
- Tone Analyzer: Discover, understand, and revise the language tones in text
- Visual Recognition: Analyze visual appearance of images to understand their contents

### Import and deploy the COVID-19 crisis communication chatbot example

Now that you have installed the Node-RED dependencies, Watson nodes are available to integrate Watson AI services. Let's build a voice enabled chatbot example that uses Watson Assistant, Watson Speech to Text and Watson Text to Speech.

Import this [flow](./flows/Node-RED-Voice-Enabled-Chatbot.json) and **Deploy** the flow.

![Node-RED flow](./images/Node-RED-COVIDChatBot-flow.png)

### Create Watson Services on IBM Cloud

Before the flow will execute successfully, you must configure the Watson Assistant and Watson Speech nodes with new service instances and API Keys.

#### Create a Watson Assistant instance

1. If you have created a [Watson Assistant service instance](https://cloud.ibm.com/catalog/services/watson-assistant). Kindly follow a), b), and c).

   ![IBM Cloud Catalog Watson Assistant](../assistant/images/WA-Photo1.png)

   * a) Copy the Watson Assistant instance API key (a) and URL (b)

      ![URL and API key](./images/watson_assistant_url.png)

   * b) Go to View API Details 

      ![Skill details](./images/skill_id.png)

   * c) Copy the Skill ID 

      ![Skill details](./images/skill_id_and_api.png)

2. If you haven't created [Watson Assistant service instance](https://cloud.ibm.com/catalog/services/watson-assistant), follow these [instructions](../assistant/README.md) to provision a Watson Assistant chatbot for COVID-19

#### Create a Watson Speech to Text service instance

1. Create a [Watson Speech to Text Service instance](https://cloud.ibm.com/catalog/services/speech-to-text) instance from the IBM Cloud Catalog. Click **Create**.
   
   ![Create Watson Speech to Text service instance](./images/Create-Watson-STT.png)

 The Node-RED Watson Speech to Text node will need the `apikey` credentials for this new instance.

2. Once the Watson Speech to Text service is created, click **Service credentials** (1).
3. Click the **View credentials** (2) twistie.
4. Copy the **apikey** (3) for use in the next section
   ![Watson STT credentials](./images/Watson-STT-apikey.png)  

#### Create a Watson Text to Speech service instance

Create a [Watson Text to Speech Service](https://cloud.ibm.com/catalog/services/text-to-speech) instance from the IBM Cloud Catalog.

1. Click **Create**.
   ![Create Watson Tex text Speech service instance](./images/Create-Watson-TTS.png)

 The Node-RED Watson Text to Speech node will need the apikey credentials for this new instance

2. After you create the Watson Text to Speech service, click **Service credentials** (1).
3. Click the **View credentials** (2) twistie.
4. Copy the **apikey** (3) for use in the next section.
   ![Watson TTS credentials](./images/Watson-TTS-apikey.png)  

### Enable Watson Speech Nodes with API keys

1. Double click the **speech to text** node and paste the API key from the Watson Speech to Text service instance.
2. Click **Done**.
   ![Config Watson STT Node](./images/Config-Watson-STT-node.png)

3. Double-click the **text to speech** node and paste the API key from the Watson Text to Speech service instance.
4. Click **Done**.
   ![Config Watson TTS Node](./images/Config-Watson-TTS-node.png)

### Enable Watson Assistant node with the COVID-19 Workspace ID and API key

1. Double-click the **assistant** node and paste the Skill ID into the Workspace ID field, Assistant Service Endpoint URL, and the API key from the Watson Assistant service instance.

2. Click **Done**.
   ![Config Watson Assistant Node](./images/Config-Watson-Assistant-node.png)

### Parse intents and invoke API calls

Watson Assistant returns the intents related to your questions.

- The `Switch` node routes two intents to an `http request` node to query an external data source for current COVID statistics.
- A `Function` node adds up the summary statistics and builds a sentence to be spoken.

### Deploy the Node-RED flows

- Click on the Red **Deploy** button.
- To view your Dashboard, go to Menu, View and select Dashboard

   ![dashboard](images/menu_dashboard.png)

- then Click on the highlighted icon (a) to launch the dashboard

   ![dashboard](images/launch_dashboard.png)

### Talk to your COVID-19 crisis chatbot

To talk to your chatbot, click on the **microphone** input button and ask a question about COVID-19.

![Node-RED Chatbot Dashboard](./images/Node-RED-COVIDChatBot-Dashboard.png)

When someone asks a question, here's the Node-RED flow that happens in the background:

![Node-RED flow](./images/Talk2COVIDChatBot.png)

### Build a Node-RED COVID statistics dashboard

**Bonus**: This flow includes a Node-RED dashboard with several gauges to display COVID-19 statistics.

![Node-RED COVID Data Dashboard](./images/Node-RED-COVID-Dashboard.png)

- Every hour, the Node-RED flow will call the [covid19api](https://api.covid19api.com/summary) summary API and collect dynamic COVID-19 infection statistics.
- The country data is aggregated and then the gauges are updated.

### Learn more about the dashboard code

The following Node-RED flow is included in this tutorial:

![Node-RED COVID Data Dashboard](./images/Node-RED-COVID-Dashboard-flow.png)

The `http request` node uses the [public Covid-19 API](https://api.covid19api.com/summary) to retrieve the daily information for all countries with infections.

Here's the sample JSON object from the summary API:

```json
{"Country":"US","Slug":"us","NewConfirmed":18058,"TotalConfirmed":83836,"NewDeaths":267,"TotalDeaths":1209,"NewRecovered":320,"TotalRecovered":681},
```

Each `function` node then aggregates the Total Confirmed Cases, Total Fatalities, Total Recovered, and Total Countries and sends the results to the corresponding `gauge` node.

This is the code in the `function` node:

```javascript
let totalConfirmedCase = 0;

msg.payload.Countries.map(function(line){
    totalConfirmedCase += line.TotalConfirmed;
});

msg.payload = totalConfirmedCase;

return msg;
```

You can expand the above dashboard to include daily new infections from [covid19api](https://api.covid19api.com/summary) data, charts, tables, and the chatbot UI.

## Congratulations on building a Call for Code Crisis Communication solution!

Awesome! You have now completed the tutorial on building a chatbot with Node-RED on IBM Cloud! Feel free to modify and enhance your solutions with other services on IBM Cloud. We are looking forward to your exciting solutions!

