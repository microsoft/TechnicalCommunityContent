# DEMO 1.1: Exploring IoT Solutions

This demo should take about 10 minutes.

## Objectives

The goal is to demonstrate an IoT solution in action (that uses Azure IoT Hub, Stream Analytics & PowerBI) and that even smartphones can be IoT devices. You’ll have the audience point their smartphone browsers to a Web page, have them send telemetry to the cloud and WOW them with live data being displayed in charts.

During this demo, there’s no need to show under the hood, understand the code or explain how the solution is built.

There are a fair number of pages/steps to get this session set up (but a lot of it is pictures), and in doing so you’ll learn plenty that can build your experience and give you credibility in all the related sessions.

## Requirements

An active Azure subscription. If you don’t have an account, you can create a free trial account in just a couple of minutes. For details, see <http://azure.microsoft.com/pricing/free-trial/>

A free Power BI account is required to display the live data. Head to [www.powerbi.com](http://www.powerbi.com) and create a free account.

1.  Click on the Sign up free link at the top right of the screen.

2.  Click the Sign up button in the Power BI tile.

    <img src="./media/image1.png" width="624" height="409" />

3.  Next, enter your work email address. Note that at this moment, public email addresses like Gmail, Hotmail or Yahoo are not accepted.

    <img src="./media/image2.png" width="624" height="290" />

Visual Studio 2015 Community Edition is required only if you plan to manually deploy the Web application.

## Setup

Make sure to create your Azure and Power BI accounts before the event.

Setting up the demo takes a few minutes so make sure to create it before the presentation. Note that the you’ll reuse the same services in demo 1.3 so don’t destroy them right after the end of this demo unless you don’t plan to show demo 1.3.

### Deploy the Web App to the Cloud 

1.  Go to the Solutions folder under this session folder

2.  Copy the folder named WebDevice

3.  Paste it under your Documents folder

4.  Navigate into the pasted folder

5.  Double-click on the WebDevice.sln file, this will launch Visual Studio

6.  In the Solution Explorer, right-click on the solution and select Restore NuGet Packages. This will download the required packages from NuGet.

    <img src="./media/image3.png" width="450" height="392" />

7.  Right-Click on the solution and select Build Solution. Make sure you resolve any errors before starting the deployment process.

8.  Right-Click on the WebDevice project and select Publish

    <img src="./media/image4.png" width="406" height="308" />

9.  Select Microsoft Azure App Service

    <img src="./media/image5.png" width="624" height="493" />

10. Select your account and subscription and click on the New… button.

    <img src="./media/image6.png" width="624" height="468" />

11. Enter a Web App Name, click on Change Type and select Web App. The name needs to be globally unique.

    <img src="./media/image7.png" width="624" height="468" />

12. Enter a Resource Group name (you can use the same name you use for the Web App) and click on the New.. button to create a new App Service Plan.

    <img src="./media/image8.png" width="624" height="468" />

13. Select a data center location that is near you and the size of the plan. You can use the Free tier but B1 is highly recommended since the free tier has limited resources and is recycled at regular intervals. You can prep using the free tier, then scale to B1 right before the demo if you want.

    <img src="./media/image9.png" width="481" height="541" />

14. Next, click the Create button to create the App Service. This will take a few seconds.

    <img src="./media/image10.png" width="624" height="468" />

15. You can now publish the demo app to Azure by clicking on the Publish button.

    <img src="./media/image11.png" width="624" height="493" />

16. Visual Studio will package the Web app and deploy it. This will take a minute or two.

17. Once deployed, Visual Studio will display a message saying that the app was published successfully and a browser will launch.

    <img src="./media/image12.png" width="595" height="281" />

18. This ends the manual deployment.

### Provision Azure IoT Hub

Each attendee will point their smartphone browsers to a Web page that will simulate an IoT device by sending messages to IoT Hub. In this section, you will create an IoT Hub.

1.  Log into the Azure portal at <http://portal.azure.com>

2.  Click on the New button.

    <img src="./media/image13.png" width="445" height="315" />

3.  Type iot in the search box and select IoT Hub from the dropdown list.

    <img src="./media/image14.png" width="378" height="187" />

4.  Next, select IoT Hub in the results tile.

    <img src="./media/image15.png" width="624" height="369" />

1.  Click the Create button.

2.  Enter a globally unique name for you IoT Hub, select a pricing tier, select your subscription and *the resource group you created when you deployed the Web app*. Finally, check Pin to Dashboard and click the Create button. This will take a minute or two.

    <img src="./media/image16.png" width="624" height="598" />

3.  Once completed, you will be presented with the overview tile.

    <img src="./media/image17.png" width="624" height="644" />

### Configure the connection string

Once you have deployed the Web app and created an IoT Hub, you need to configure the Web App with the IoT Hub connection string.

1.  Select the IoT Hub that you created in the previous steps

2.  Click on the Shared access policies link, then iothubowner and click on the Copy button to the right of the first connection string. This will copy the connection string to the clipboard.

    <img src="./media/image18.png" width="624" height="293" />

3.  Locate the Web app by clicking on the App Services link in the left side menu and click on the service name.

    <img src="./media/image19.png" width="473" height="361" />

4.  Click the Application Settings link in the Settings section.

    <img src="./media/image20.png" width="624" height="528" />

5.  Scroll down a little bit and locate the App settings section.

    <img src="./media/image21.png" width="551" height="128" />

6.  Enter these two keys:

    a.  iotHubConnectionString

    b.  iotHubUri

7.  And their values (note that the iotHubUri value is the HostName part of the connection string). Here are some examples:

    a.  HostName=IoTHubSimulatedDevices.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=oK9DY2WPVAK4mpZ2FB+3Akq5CPULizOP/hHqNuXQiUM=

    b.  IoTHubSimulatedDevices.azure-devices.net

8.  You should have something like this. The sections highlighted in yellow will be specific to your own configuration.

  iotHubConnectionString   HostName=IoTHubSimulatedDevices.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=oK9DY2WPVAK4mpZ2FB+3Akq5CPULizOP/hHqNuXQiUM=
  ------------------------ ------------------------------------------------------------------------------------------------------------------------------------------------
  iotHubUri                IoTHubSimulatedDevices.azure-devices.net

1.  Click on Save.

    <img src="./media/image22.png" width="559" height="262" />

2.  The app settings are now set.

### Configure Stream Analytics

The messages sent to IoT Hub will be read and analysed using Stream Analytics.

1.  Click on the portal hamburger menu, then New, the type “stream analytics” and select Stream Analytics Jobs from the dropdown menu.

    <img src="./media/image23.png" width="381" height="258" />

2.  Select Stream Analytics Job from the result tile and click the Create button.

    <img src="./media/image24.png" width="617" height="395" />

3.  Type a job name, select your Azure subscription and *select the resource group that you created earlier*.

    <img src="./media/image25.png" width="309" height="339" />

4.  Click the Create button. This will take a few seconds.

5.  Click on the Inputs tile.

    <img src="./media/image26.png" width="624" height="416" />

1.  Click Add

    <img src="./media/image27.png" width="253" height="173" />

2.  In the New Input tile, type an Input Alias name, select IoT Hub from the Source dropdown list and make sure the IoT Hub you created earlier is selected. Click Create.

    <img src="./media/image28.png" width="231" height="670" />

3.  Let’s now create an output. Click the Outputs tile.

    <img src="./media/image29.png" width="624" height="430" />

4.  Click Add.

    <img src="./media/image30.png" width="226" height="178" />

5.  From the Sink dropdown and select Power BI. The tile will display new fields automatically depending on the selected sink.

    <img src="./media/image31.png" width="303" height="405" />

6.  Type a name for your output and the dataset and table names. Click Create.

    <img src="./media/image32.png" width="292" height="389" />

7.  Next, click on the Query tile.

    <img src="./media/image33.png" width="624" height="421" />

8.  In the left section of the Query tile you’ll see the input and output you created earlier. The right section shows a skeleton query.

    <img src="./media/image34.png" width="624" height="290" />

9.  Type or copy/paste the following query and replace the placeholders with the names of your input and output.

10. You should have some similar then this:

    <img src="./media/image35.png" width="574" height="184" />

11. Click on Save.

    <img src="./media/image36.png" width="226" height="268" />

12. Dismiss the Query tile and *click on Start to start the stream analytics job*.

    <img src="./media/image37.png" width="590" height="280" />

13. Select Now as the start time. This will take a few minutes.

    <img src="./media/image38.png" width="549" height="126" />

14. The Stream Analytics job is now set.

### Test what you’ve done so far

It’s now time to do a little test to make sure that everything you’ve configured so far does work. Remember what you have configured: a Web site that will send messages to an IoT Hub instance and a Stream Analytics job that will query the IoT Hub in real time and ultimately provide the results to Power BI.

1.  Do you remember the URL for the Web app you deployed? No? No worries! Click on the portal hamburger menu and click on App Services.

    <img src="./media/image39.png" width="224" height="328" />

2.  Click the name of your Web app and you’ll find the URL in the tile on the right. Click the URL.

    <img src="./media/image40.png" width="624" height="169" />

3.  A browser will launch displaying the site’s home page. Note the URL because you’ll direct the attendees to this page.

    <img src="./media/image41.png" width="624" height="299" />

4.  Click on the Go to Demo 1 button.

    <img src="./media/image42.png" width="624" height="230" />

5.  A unique device ID is created each time this page is loaded. Displayed is it’s unique name and the key unique to that device. The ID and the key are used by IoT Hub.

6.  You’ll see a slider where you can set values from 0 to 100.

7.  The On/Off button is used to start/stop the sending or not of the fake telemetry to the backend. A message is sent every 2 seconds.

8.  Click on the On/Off button and move the slider. You’ll start seeing values displayed in a table, with the newest one at the top.

    <img src="./media/image43.png" width="624" height="354" />

9.  Let’s now confirm that IoT Hub did receive the messages we sent. Head back to your IoT Hub tile in the Azure Portal. In the Usage section, you will see that you have one device registered and the number of messages received. Click on the Devices button. This will open the Device Explorer tile and you’ll see the device that was registered by launching the Demo 1 page.

    <img src="./media/image44.png" width="624" height="348" />

10. Awesome! Now let’s configure a report in Power BI.

### Configure Power BI

The last step to prepare this demo is to configure a report in Power BI.

1.  Head to [www.powerbi.com](http://www.powerbi.com)

2.  You should see a new dataset with the name you typed earlier in the Stream Analytics output configuration. Click on the dataset name.

    <img src="./media/image45.png" width="354" height="512" />

3.  To the right of the screen, you’ll see the table name and the fields names along with a series of visualizations or chart types. Select the Card visualization and check the deviceid checkbox.

    <img src="./media/image46.png" width="363" height="436" />

4.  We want to display the number of devices so let’s change the count to a distinct count by selecting Count (Distinct) in the Fields section for our deviceid field.

    <img src="./media/image47.png" width="339" height="391" />

5.  The count is displayed in the card.

    <img src="./media/image48.png" width="574" height="509" />

6.  Let’s now create a line chart to display the average telemetry for all devices over time.

7.  Make sure the card is not selected anymore, otherwise it will be changed into the new visualization.

7.  Click on the Line visualization, select the avgtelemetry and outtime fields. Make sure that outtime is in the Axis section (you can drag&drop it if needed) and that avgtelemetry is in the Values section. *Select Average from the dropdown menu to average the avgtelemetry field.*

    <img src="./media/image49.png" width="349" height="644" />

8.  You should have something like this:

    <img src="./media/image50.png" width="311" height="332" />

9.  Let’s display these two reports in a new dashboard.

10. Click the pin button on each graph.

    <img src="./media/image51.png" width="314" height="240" />

11. Power BI will prompt you for a report name.

    <img src="./media/image52.png" width="624" height="303" />

12. Next, Power BI will ask you if you want to pin the report in a new or existing dashboard. Select New and enter a name.

    <img src="./media/image53.png" width="624" height="304" />

13. Notice that the new dashboard appears in the Dashboard section. Select it.

    <img src="./media/image54.png" width="217" height="429" />

14. Congratulations! You just built your first Power BI dashboard and are ready to perform the demos per the instructions found for each demo.

    <img src="./media/image55.png" width="624" height="345" />

## Demo Steps

Explain to the attendees that they’ll use their smartphone as simulated devices that will send some fake telemetry to a cloud backend and the data will be displayed in charts live.

Before beginning your presentation:

1.  Point your browser to the Azure portal: <http://portal.azure.com>

2.  Log in

3.  In a new tab, point to http://www.powerbi.com

4.  Login

5.  In a new tab, display the demo Web App

Let’s start the demo:

1.  Show demo Web app.

2.  Give the URL to the attendees.

    <img src="./media/image41.png" width="624" height="299" />

3.  Ask them to click on the Go to Demo 1 button.

4.  Explain that the page will send to the cloud backend the number that they will select using the slider, and that, every two seconds.

5.  Ask them to click on the toggle button and move the slider from time to time.

    <img src="./media/image43.png" width="624" height="354" />

6.  Show the Power BI dashboard and explain that the data is displayed in almost real time.

    <img src="./media/image55.png" width="624" height="345" />

7.  Show the IoT Hub in the Azure Portal.

8.  Explain that the messages are sent to an IoT messaging service called Azure IoT Hub that can process thousands of messages per second.

9.  Show the Stream Analytics job.

10. Show the query.

11. Explain that the query runs every 2 seconds and average the values sent by each device.

12. Explain that the query outputs the results to Power BI directly.

<!-- -->

1.  Create a new graph. Click on the SimulatedDevice Dataset.

2.  Select the line graph, drag the outtime field as the Axis, drag the avgtelemetry field as the Value and select Average from it’s dropdown menu.

    <img src="./media/image56.png" width="366" height="541" />

3.  Drag the deviceid field as the Page level filter and select one of the device.

    <img src="./media/image57.png" width="160" height="235" />

4.  If the device you’re looking for is not listed, select Advanced filtering, contains and the device name. Click Apply filter.

    <img src="./media/image58.png" width="170" height="335" />

5.  Save and pin the report to the dashboard.

6.  Ask the attendee you have this device to change the value from time to time.

    <img src="./media/image59.png" width="624" height="453" />

7.  This concludes the demo.

## Teardown

You’ll be reusing the same services in demo 1.3 so don’t delete them yet. See Teardown in 1.3 you have finished the session. *Be sure to do this to avoid excess resource charges.*
