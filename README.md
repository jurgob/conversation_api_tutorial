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



If everything is gonna work correctly, You should see the `Who am I?` text while the client is connecting, once connected is gonna be replaced by your username


## add a backend


go back to your workspace and clone https://github.com/jurgob/vapi_hello_world

```
cd ..

git clone https://github.com/jurgob/vapi_hello_world

cd vapi_hello_world

```
read the readme here: https://github.com/jurgob/vapi_hello_world and be sure your inbound call is working


with this project you can also create a user, let's crete a user called `myuser` 

```

npm run cli_create_user myuser

```


now go back in the `nexmo_client_sdk_react_tutorial` project and change the src/App.js file again: 

```
cd ..
cd nexmo_client_sdk_react_tutorial

```

change src/App.js with: 


```
import React from 'react';
import axios from 'axios';

import './App.css';

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



class LoggedArea extends React.Component {
  constructor(){
    super();
    this.state = {
      token: "",
      username: "",
      errorMsg: ""
    }

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({username: event.target.value});
  }

   handleSubmit(event) {
    // alert('A name was submitted: ' + this.state.value);
    axios.post(`http://localhost:5000/tokens/${this.state.username}`)
      .then(getTokenRes => {
        this.setState({
          token: getTokenRes.data.token,
          username: "",
          errorMsg: ""
        })
      })
      .catch(err => {
        console.log(`err`, err)
        this.setState({
          token: "",
          errorMsg: err
        })
      })
    event.preventDefault();
  }

  render() {
    // return <div>cane</div>
    const {token, errorMsg} = this.state;
    const loginForm = (<form onSubmit={this.handleSubmit}>
        <label>
          user name:
          <input type="text" name="username" value={this.state.username} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Login" />
      </form>)

    return (
      <div>
        { errorMsg && <div className="errorMsg" > { "errorMsg" }</div> }
        { token ? <NexmoClientWidget token={token}  /> : loginForm }
      </div>
    )

  }

}

1

function App() {

  return (
    <div className="App">
      <LoggedArea />
    </div>
  );
}

export default App;

```

now on localhost:3000 you shoudl see a form asking for a user name. 

open another terminal, go to the vapi_hello_world project and run it.

now if you login in as `myuser` you should see your username. Now the nexmo SDK is connected!




