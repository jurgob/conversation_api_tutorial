# conversation_api_tutorial

## install nvm

```
curl -o- 
https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash

nvm install v12.16.1

npx create-react-app nexmo_client_sdk_react_tutorial

cd nexmo_client_sdk_react_tutorial

echo "v12.16.1" > .nvmrc
```


## install deps
```  npm install --save nexmo-client ```

## run your app
``` npm start ```

now you should see a working app on `http://localhost:3000/`




## now let's create a nexmo hello world

set the content of the file src/App.js

```
import React from 'react';
import './App.css';


const TOKEN = "<YOUR_TOKEN>"


const Me = ({me}) => {
  if (!me)
    return <div>Who am I?</div>
 return <div>{me.name}</div>
}


class NexmoClientWidget extends React.Component {
  constructor(){
    super();
    this.state = {
      me: null,
      nexmoApp: null
    }
  }

  componentDidMount() {
    const isServer = typeof window === 'undefined'
    const NexmoClient = !isServer ? require('nexmo-client') : null
    if(NexmoClient){
      
      const nexmoClient = new NexmoClient({ debug: false })
      
      nexmoClient
        .login(this.props.token)
        .then(nexmoApp => {
          console.log(`app: `, nexmoApp)
          window.nexmoApp = nexmoApp;
          this.setState((state, props) => {
            return {
              ...this.state,
              me: {
                name: nexmoApp.me.name,
                id: nexmoApp.me.id
              },
              nexmoApp: nexmoApp

            }
          })

          nexmoApp.on('*', (event, evt) => {
            console.log("event: ", event, evt)
            console.log('nexmoApp.activeStreams.length ', nexmoApp.activeStreams.length)
          }

            )

        })

    }
  }

  render() {
    const {nexmoApp} = this.state
    return (
      <div>
        <h2>ClientSDK Tutorial </h2>
        <Me me={this.state.me} />

      </div>
    );

  }
}




function App() {
  return (
    <div className="App">
      <NexmoClientWidget token={TOKEN}  />
    </div>
  );
}

export default App;

```

