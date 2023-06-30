# How to Build React Video Calling App with Video SDK
### Introduction
Do you find the existing Video Calling apps to be limiting? If yes, why not make your own video calling app that you can use for anything. With the help of [Video SDK](https://www.videosdk.live/), It is very easy to integrate and build your own video calling app with features like chat, poll, whiteboard and much more.

Video SDK is a platform that allows developers to create rich in-app experiences such as embedding real-time video, voice, real-time recording, live streaming and real-time messaging. Video SDK is available in [JavaScript](https://docs.videosdk.live/javascript/guide/video-and-audio-calling-api-sdk/quick-start), [Reactjs](https://docs.videosdk.live/react/guide/video-and-audio-calling-api-sdk/quick-start), [React-Native](https://www.videosdk.live/blog/how-to-make-a-video-calling-app-using-react-native), [iOS](https://docs.videosdk.live/ios/guide/video-and-audio-calling-api-sdk/quick-start), [Android](https://docs.videosdk.live/android/guide/video-and-audio-calling-api-sdk/getting-started) and [Flutter](https://www.videosdk.live/blog/video-calling-in-flutter) to be seamlessly integrated. Video SDK also provides a [pre-built SDK](https://docs.videosdk.live/prebuilt/guide/prebuilt-video-and-audio-calling/getting-started), which enables you to integrate real time communication with your application in just 10 minutes.

In this tutorial, You are going to explore the group calling feature of Video SDK. You will go through step by step guide on integrating video calling with [React](https://en.wikipedia.org/wiki/React_(software)) Video SDK.

This guide will get you running with the Video SDK video & audio calling in minutes.

Let us create a video calling app using ReactJS and Video SDK.

## Prerequisites
Before proceeding, ensure that your development environment meets the following requirements:

- Video SDK Developer Account (Not having one? follow [Video SDK Dashboard](https://app.videosdk.live/))
- Basic understanding of React
- [React Video SDK](https://www.npmjs.com/package/@videosdk.live/react-sdk)
- Have [Node](https://en.wikipedia.org/wiki/Node.js) and NPM installed on your device
- The basic understanding of Hooks (useState, useRef, useEffect)
- React Context API (optional)

> One should have a Video SDK account to generate tokens. Visit Video SDK [dashboard](https://app.videosdk.live/api-keys) to generate a token

## Getting Started with the Code!
Follow the steps to create the environment necessary to add video calls to your app.

### Create a new react app
Create a new React App using the below command

```js
$ npx create-react-app videosdk-rtc-react-app
```

### Install Video SDK
Install the Video SDK using the below-mentioned npm command. Make sure you are in your react app directory before you run this command.

```js
$ npm install "@videosdk.live/react-sdk"

//For the Participants Video
$ npm install "react-player"
```
### Structure of the project
Your project structure should look like this after creating the app with create-react-app

```js
root
   ├── node_modules
   ├── public
   ├── src
   │    ├── api.js
   │    ├── App.js
   │    ├── App.css
   │    ├── index.js
   │    ├── index.js
   .    .
```

We are going to use functional components to leverage React's reusable component architecture. There will be components for users, videos and controls (mic, camera, leave) over the video.

### App Architecture
App will contain a MeetingView component which includes ParticipantView which will render the participant's name, video, audio, etc. We will also have a Controls component which will allow user to perform operations like leave and toggle media.

We are going to work on two files:

- API.js: Responsible to handle API calls such as generating unique meetingId and token
- App.js: Responsible to render MeetingView and join the meeting.

## 5 Steps to build a React video call app
### Step 1: Get started with API.js
Prior to moving on, we must create an API request to generate unique meetingId. You will need an authentication token, which you can create either through the [videosdk-rtc-api-server-examples](https://github.com/videosdk-live/videosdk-rtc-api-server-examples) or directly from the [Video SDK Dashboard](https://app.videosdk.live/api-keys) for developers.

```js
//Auth token we will use to generate a meeting and connect to it
export const authToken = "<Generated-from-dashbaord>";
// API call to create meeting
export const createMeeting = async ({ token }) => {
  const res = await fetch(`https://api.videosdk.live/v2/rooms`, {
    method: "POST",
    headers: {
      authorization: `${authToken}`,
      "Content-Type": "application/json",
    },
    body: JSON.stringify({}),
  });
  //Destructuring the roomId from the response
  const { roomId } = await res.json();
  return roomId;
};
```
### Step 2: Wireframe App.js with all the components
To build up a wireframe of App.js, we are going to use Video SDK Hooks and Context Providers. Video SDK provides MeetingProvider, MeetingConsumer, useMeeting and useParticipant hooks. Let's understand each of them.

First, we will explore Context Provider and Consumer. Context is primarily used when some data needs to be accessible by many components at different nesting levels.

- MeetingProvider: It is Context Provider. It accepts value config and token as props. The Provider component accepts a value prop to be passed to consuming components that are descendants of this Provider. One Provider can be connected to many consumers. Providers can be nested to override values deeper within the tree.
- MeetingConsumer: It is Context Consumer. All consumers that are descendants of a Provider will re-render whenever the Provider’s value prop changes.
- useMeeting: It is meeting react hook API for a meeting. It includes all the information related to the meeting such as participants, streams etc.
- useParticipant: It is participant hook API. useParticipant hook is responsible to handle all the events and props related to one particular participant such as join, leave, mute etc.

Meeting Context helps to listen to all the changes when a participant joins a meeting or changes Mic or Camera etc.

Let's get started with change a couple of lines of code in App.js.

```js
import "./App.css";
import React, { useEffect, useRef, useState } from "react";
import {
  MeetingProvider,
  MeetingConsumer,
  useMeeting,
  useParticipant,
} from "@videosdk.live/react-sdk";
import { authToken, createMeeting } from "./API";

function JoinScreen() {
  return null;
}

function VideoComponent(props) {
  return null;
}

function Controls(props) {
  return null;
}

function Container(props) {
  return null;
}

function App() {
  const [meetingId, setMeetingId] = useState(null);

  const getMeetingAndToken = async (id) => {
    const meetingId =
      id == null ? await createMeeting({ token: authToken }) : id;
    setMeetingId(meetingId);
  };

  return authToken && meetingId ? (
    <MeetingProvider
      config={{
        meetingId,
        micEnabled: true,
        webcamEnabled: false,
        name: "C.V. Raman",
      }}
      token={authToken}
    >
      <MeetingConsumer>
        {() => <Container meetingId={meetingId} />}
      </MeetingConsumer>
    </MeetingProvider>
  ) : (
    <JoinScreen getMeetingAndToken={getMeetingAndToken} />
  );
}

export default App;
```
### Step 3: Implement Join Screen
Join screen will work as a medium to either schedule a new meeting or to join an existing meeting.

```js
function JoinScreen({ getMeetingAndToken }) {
  const [meetingId, setMeetingId] = useState(null);
  const onClick = async () => {
    await getMeetingAndToken(meetingId);
  };
  return (
    <div>
      <input
        type="text"
        placeholder="Enter Meeting Id"
        onChange={(e) => {
          setMeetingId(e.target.value);
        }}
      />
      <button onClick={onClick}>Join</button>
      {" or "}
      <button onClick={onClick}>Create Meeting</button>
    </div>
  );
}
```

### Step 4: Implement Container and Controls
Next step is to create MeetingView and Controls components to manage features such as join, leave, mute and unmute.

```js
function MeetingView(props) {
  const [joined, setJoined] = useState(null);
  //Get the method which will be used to join the meeting.
  //We will also get the participants list to display all participants
  const { join, participants } = useMeeting({
    //callback for when meeting is joined successfully
    onMeetingJoined: () => {
      setJoined("JOINED");
    },
    //callback for when meeting is left
    onMeetingLeft: () => {
      props.onMeetingLeave();
    },
  });
  const joinMeeting = () => {
    setJoined("JOINING");
    join();
  };

  return (
    <div className="container">
      <h3>Meeting Id: {props.meetingId}</h3>
      {joined && joined == "JOINED" ? (
        <div>
          <Controls />
          //For rendering all the participants in the meeting
          {[...participants.keys()].map((participantId) => (
            <ParticipantView
              participantId={participantId}
              key={participantId}
            />
          ))}
        </div>
      ) : joined && joined == "JOINING" ? (
        <p>Joining the meeting...</p>
      ) : (
        <button onClick={joinMeeting}>Join</button>
      )}
    </div>
  );
}
```
Apart from that Control Component will be required to handle user actions.

```js
function Controls() {
  const { leave, toggleMic, toggleWebcam } = useMeeting();
  return (
    <div>
      <button onClick={leave}>Leave</button>
      <button onClick={toggleMic}>toggleMic</button>
      <button onClick={toggleWebcam}>toggleWebcam</button>
    </div>
  );
}
```
### Step 5: Implement Participant View
Before implementing the video component, We need to understand a couple of concepts.

**1. Forwarding Ref for mic and camera**
Ref forwarding is a technique for automatically passing a ref through a component to one of its children. We are going to use Refs to attach audio and video tracks with components.

```js
const webcamRef = useRef(null);
const micRef = useRef(null);
```

**2. useParticipant Hook​**
useParticipant hook is responsible to handle all the properties and events of one particular participant joined in the meeting. It will take participantId as an argument.

```js
const { webcamStream, micStream, webcamOn, micOn } = useParticipant(
  props.participantId
);
```

**3. MediaStream API**
MediaStream is useful to add MediaTrack to the audio / video tag to play the audio or video.​

```js
const webcamRef = useRef(null);
const mediaStream = new MediaStream();
mediaStream.addTrack(webcamStream.track);

webcamRef.current.srcObject = mediaStream;
webcamRef.current
  .play()
  .catch((error) => console.error("videoElem.current.play() failed", error));
```
Now let's use all these APIs to create ParticipantView​

```js
function ParticipantView(props) {
  const micRef = useRef(null);
  const { webcamStream, micStream, webcamOn, micOn, isLocal, displayName } =
    useParticipant(props.participantId);

  const videoStream = useMemo(() => {
    if (webcamOn && webcamStream) {
      const mediaStream = new MediaStream();
      mediaStream.addTrack(webcamStream.track);
      return mediaStream;
    }
  }, [webcamStream, webcamOn]);

  useEffect(() => {
    if (micRef.current) {
      if (micOn && micStream) {
        const mediaStream = new MediaStream();
        mediaStream.addTrack(micStream.track);

        micRef.current.srcObject = mediaStream;
        micRef.current
          .play()
          .catch((error) =>
            console.error("videoElem.current.play() failed", error)
          );
      } else {
        micRef.current.srcObject = null;
      }
    }
  }, [micStream, micOn]);

  return (
    <div>
      <p>
        Participant: {displayName} | Webcam: {webcamOn ? "ON" : "OFF"} | Mic:{" "}
        {micOn ? "ON" : "OFF"}
      </p>
      <audio ref={micRef} autoPlay playsInline muted={isLocal} />
      {webcamOn && (
        <ReactPlayer
          //
          playsinline // very very imp prop
          pip={false}
          light={false}
          controls={false}
          muted={true}
          playing={true}
          //
          url={videoStream}
          //
          height={"300px"}
          width={"300px"}
          onError={(err) => {
            console.log(err, "participant video error");
          }}
        />
      )}
    </div>
  );
}
```

We are done with implementation of customized video calling app in react js using Video SDK. To explore more features go through [Basic](https://docs.videosdk.live/react/guide/video-and-audio-calling-api-sdk/setup-call/initialise-meeting#) and [Advanced](https://docs.videosdk.live/react/guide/video-and-audio-calling-api-sdk/plugins/virtual-background) features.

## Conclusion
We have successfully completed the video calling app using ReactJS. If you wish to add functionalities like chat messaging, screen sharing, polls etc, you can always check out our [documentation](https://docs.videosdk.live/). If you face any difficulty with the implementation, you can check out the [example on GitHub](https://github.com/videosdk-live/videosdk-rtc-react-sdk-example) or connect with us on our [discord community](https://discord.gg/Gpmj6eCq5u).

## More React Resources

- [React Audio/Video call documentation](https://docs.videosdk.live/react/guide/video-and-audio-calling-api-sdk/quick-start)
- [React interactive live streaming documentation](https://docs.videosdk.live/react/guide/video-and-audio-calling-api-sdk/quick-start-ILS)
- [React interactive live streaming blog](https://www.videosdk.live/blog/react-interactive-live-streaming)
- [React Audio/Video call example code](https://github.com/videosdk-live/videosdk-rtc-react-sdk-example)
- [React live streaming example code](https://github.com/videosdk-live/videosdk-live-streaming-react-api-example)
