<svelte:head>
    <title>API Test</title>
</svelte:head>

<style>
  /* width */
  ::-webkit-scrollbar {
    width: 20px;
  }

  /* Track */
  ::-webkit-scrollbar-track {
    box-shadow: inset 0 0 5px grey; 
    border-radius: 10px;
  }
  
  /* Handle */
  ::-webkit-scrollbar-thumb {
    background: red; 
    border-radius: 10px;
  }

  /* Handle on hover */
  ::-webkit-scrollbar-thumb:hover {
    background: #b30000; 
  }

  .responsesstyles{
    background-color: antiquewhite;
    padding: 10px;
    border-radius: 10px;
    box-shadow: 0px 0px 3px 0px black;
    font-weight: bold;
    font-family: Arial, Helvetica, sans-serif;
  }
</style>

<script>
  // @ts-nocheck
    import HeaderComponent from '$lib/MyComponents/HeaderComponent.svelte';
    import FooterComponent from '$lib/MyComponents/FooterComponent.svelte';
    import axios from 'axios'
    let APIMethod = "GET";
    let URL = "";
    let ResponseHeaders;
    let ResponseData;
    let ErrorMessage;
    let Loader = false;
    let bodyKey = ""
    let bodyvalue = ""
    let APIBody = {}
    let HeaderKey = ""
    let Headeralue = ""
    let HeaderBody = {}
    let Postbodytext = ""
    let Alertbox = false;
    // @ts-ignore
    const changeMethod = (e) => {
        APIMethod = e.target.innerHTML
    }

    const AddKeyValuePair = () => {
        APIBody[bodyKey] = bodyvalue
    }

    const RemoveKeyValuePair = () => {
        delete(APIBody[bodyKey]);
        APIBody = APIBody
    }

    const AddKeyValuePairHeader = () => {
      HeaderBody[HeaderKey] = Headeralue
    }

    const RemoveKeyValuePairHeader = () => {
        delete(HeaderBody[HeaderKey]);
        HeaderBody = HeaderBody
    }

    const ChangesPostBody = () => {
      try
      {
      let txt = document.getElementById("txtpostmeth").value;
      APIBody = JSON.parse(txt);
      }
      catch(e){
        APIBody = {}
      }
    }

    const ClearFunction = () => {
      APIMethod = "GET";
      URL = "";
      ResponseHeaders = undefined;
      ResponseData = undefined;
      ErrorMessage = undefined;
      Loader = false;
      bodyKey = ""
      bodyvalue = ""
      APIBody = {}
      Postbodytext = ""
      HeaderKey = ""
      Headeralue = ""
      HeaderBody = {}
    }

    const sendFunc = async () => {
      Loader=true;
      document.getElementById("pills-contact-tab").click()
      if(URL)
      {
        if(APIMethod == "GET"){
          try{
            let Body = {APIURL:URL}
            axios.post("https://apiserver-roan.vercel.app/GET_ENDPOINT",Body,{headers:HeaderBody}).
            then((responses) => {
              // console.log(responses)
              if(responses)
              {
                if(responses.status == 200){
                ResponseHeaders = JSON.stringify(responses.data.RequestHeaders,null,3);
                ResponseData = JSON.stringify(responses.data.RequestResponse,null,3);
                }
                else{
                  ResponseData = undefined;
                  ResponseHeaders = undefined;
                }
              }
            })
            .catch((err)=>{
              //console.error(err)
              ErrorMessage = `Error: ${err.message}`;
              ResponseData = undefined;
              ResponseHeaders = undefined;
            })
          }
          catch(err){
            ResponseData = undefined;
            ResponseHeaders = undefined;
          }
        }
        if(APIMethod == "POST"){
          try{
            let Body = {APIURL:URL,data:APIBody,Headers:HeaderBody}
            axios.post("https://apiserver-roan.vercel.app/POST_ENDPOINT",Body).
            then((responses) => {
              // console.log(responses)
              if(responses)
              {
                if(responses.status == 200){
                ResponseHeaders = JSON.stringify(responses.data.RequestHeaders,null,3);
                ResponseData = JSON.stringify(responses.data.RequestResponse,null,3);
                }
                else{
                  ResponseData = undefined;
                  ResponseHeaders = undefined;
                }
              }
            })
            .catch((err)=>{
              //console.error(err)
              ErrorMessage = `Error: ${err.message}`;
              ResponseData = undefined;
              ResponseHeaders = undefined;
            })
          }
          catch(err){
            ResponseData = undefined;
            ResponseHeaders = undefined;
          }
        }
      }
      else{
        Alertbox = true;
      }
      Loader=false;
    }
</script>
<HeaderComponent />
  {#if Alertbox}
  <div class="alert alert-danger alert-dismissible fade show" role="alert">
    <strong>Please enter the URL in Textfield!</strong>
    <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close" on:click={() => {Alertbox = false}}></button>
  </div>
  {/if}
<div class="container-fluid">
    <div class="input-group mb-3" style="padding-top:10px">
        <button class="btn btn-success fw-bold dropdown-toggle" type="button" data-bs-toggle="dropdown" aria-expanded="false">{APIMethod}</button>
        <ul class="dropdown-menu">
          <li><button class="dropdown-item fw-bold" on:click={changeMethod}>GET</button></li>
          <li><button class="dropdown-item fw-bold" on:click={changeMethod}>POST</button></li>
        </ul>
        <input type="text" class="form-control" placeholder="Enter URL..." bind:value={URL} aria-label="Text input with dropdown button">
        <button type="button" class="btn btn-outline-success" on:click={sendFunc}>SEND <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrow-right-circle-fill" viewBox="0 0 16 16">
          <path d="M8 0a8 8 0 1 1 0 16A8 8 0 0 1 8 0M4.5 7.5a.5.5 0 0 0 0 1h5.793l-2.147 2.146a.5.5 0 0 0 .708.708l3-3a.5.5 0 0 0 0-.708l-3-3a.5.5 0 1 0-.708.708L10.293 7.5z"/>
        </svg></button>
        <button class="btn btn-outline-success" on:click={ClearFunction}>Clear <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-x-circle-fill" viewBox="0 0 16 16">
          <path d="M16 8A8 8 0 1 1 0 8a8 8 0 0 1 16 0M5.354 4.646a.5.5 0 1 0-.708.708L7.293 8l-2.647 2.646a.5.5 0 0 0 .708.708L8 8.707l2.646 2.647a.5.5 0 0 0 .708-.708L8.707 8l2.647-2.646a.5.5 0 0 0-.708-.708L8 7.293z"/>
        </svg></button>
      </div>
      <hr>
      <!-- Tabs -->
      <ul class="nav nav-pills mb-3 py-3" id="pills-tab" role="tablist">
        <li class="nav-item" role="presentation">
          <button class="nav-link active" id="pills-home-tab" data-bs-toggle="pill" data-bs-target="#pills-home" type="button" role="tab" aria-controls="pills-home" aria-selected="true">Headers</button>
        </li>
        <li class="nav-item" role="presentation">
          <button class="nav-link" id="pills-profile-tab" data-bs-toggle="pill" data-bs-target="#pills-profile" type="button" role="tab" aria-controls="pills-profile" aria-selected="false">Body</button>
        </li>
        <li class="nav-item" role="presentation">
          <button class="nav-link" id="pills-contact-tab" data-bs-toggle="pill" data-bs-target="#pills-contact" type="button" role="tab" aria-controls="pills-contact" aria-selected="false">Response</button>
        </li>
      </ul>
      <hr>

      <!-- Tab Contents -->
      <div class="tab-content" id="pills-tabContent">
        <!-- Headers -->
        <div class="tab-pane fade show active" id="pills-home" role="tabpanel" aria-labelledby="pills-home-tab" tabindex="0">
          <div>
            <div class="row">
              <div class="col">
                <input type="text" class="form-control" placeholder="Key" disabled={URL==""?true:false} bind:value={HeaderKey} aria-label="First name">
              </div>
              <div class="col">
                <input type="text" class="form-control" placeholder="Value" disabled={URL==""?true:false} bind:value={Headeralue} aria-label="Last name">
              </div>
              <div class="col">
                <button class="btn btn-success" on:click={AddKeyValuePairHeader}>➕</button>
                <button class="btn btn-success" on:click={RemoveKeyValuePairHeader}>➖</button>
              </div>
            </div>
            <div class="my-3">
              <pre class="responsesstyles">{JSON.stringify(HeaderBody,null,3)}</pre>
            </div>
          </div>
        </div>
        <!-- Request Body -->
        <div class="tab-pane fade" id="pills-profile" role="tabpanel" aria-labelledby="pills-profile-tab" tabindex="0">
          <div>
            {#if APIMethod == "POST"}
              <div>
                <div class="row">
                  <div class="col">
                    <input type="text" class="form-control" placeholder="Key" disabled={URL==""?true:false} bind:value={bodyKey} aria-label="First name">
                  </div>
                  <div class="col">
                    <input type="text" class="form-control" placeholder="Value" disabled={URL==""?true:false} bind:value={bodyvalue} aria-label="Last name">
                  </div>
                  <div class="col">
                    <button class="btn btn-success" on:click={AddKeyValuePair}>➕</button>
                    <button class="btn btn-success" on:click={RemoveKeyValuePair}>➖</button>
                  </div>
                </div>
                <br>
                <textarea class="form form-control" disabled={URL==""?true:false} cols="5" rows="6" on:change={ChangesPostBody} id="txtpostmeth">{JSON.stringify(APIBody,null,3)}</textarea>
                <hr>
                <div class="my-3">
                  <pre class="responsesstyles">{JSON.stringify(APIBody,null,3)}</pre>
                </div>
              </div>
              {:else}
              <div>
                <h6 class="responsesstyles">Only for the POST Request Body is accessable</h6>
              </div>
            {/if}
        </div>
        </div>
        <!-- Response -->
        <div class="tab-pane fade" id="pills-contact" role="tabpanel" aria-labelledby="pills-contact-tab" tabindex="0">
          {#if ResponseData}
          <div>
            <h6>Response Headers <button class="btn btn-success" on:click={navigator.clipboard.writeText(ResponseHeaders)}>Copy <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-clipboard" viewBox="0 0 16 16">
              <path d="M4 1.5H3a2 2 0 0 0-2 2V14a2 2 0 0 0 2 2h10a2 2 0 0 0 2-2V3.5a2 2 0 0 0-2-2h-1v1h1a1 1 0 0 1 1 1V14a1 1 0 0 1-1 1H3a1 1 0 0 1-1-1V3.5a1 1 0 0 1 1-1h1z"/>
              <path d="M9.5 1a.5.5 0 0 1 .5.5v1a.5.5 0 0 1-.5.5h-3a.5.5 0 0 1-.5-.5v-1a.5.5 0 0 1 .5-.5zm-3-1A1.5 1.5 0 0 0 5 1.5v1A1.5 1.5 0 0 0 6.5 4h3A1.5 1.5 0 0 0 11 2.5v-1A1.5 1.5 0 0 0 9.5 0z"/>
            </svg></button></h6>
            <pre class="responsesstyles">{ResponseHeaders}</pre>
            <hr>
            <h6>Response Body <button class="btn btn-success" on:click={navigator.clipboard.writeText(ResponseData)}>Copy <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-clipboard" viewBox="0 0 16 16">
              <path d="M4 1.5H3a2 2 0 0 0-2 2V14a2 2 0 0 0 2 2h10a2 2 0 0 0 2-2V3.5a2 2 0 0 0-2-2h-1v1h1a1 1 0 0 1 1 1V14a1 1 0 0 1-1 1H3a1 1 0 0 1-1-1V3.5a1 1 0 0 1 1-1h1z"/>
              <path d="M9.5 1a.5.5 0 0 1 .5.5v1a.5.5 0 0 1-.5.5h-3a.5.5 0 0 1-.5-.5v-1a.5.5 0 0 1 .5-.5zm-3-1A1.5 1.5 0 0 0 5 1.5v1A1.5 1.5 0 0 0 6.5 4h3A1.5 1.5 0 0 0 11 2.5v-1A1.5 1.5 0 0 0 9.5 0z"/>
            </svg></button></h6>
            <pre class="responsesstyles">{ResponseData}</pre>
          </div>
          {:else if ErrorMessage}
          <div>
            <h6>Error Message</h6>
            <pre class="responsesstyles">{ErrorMessage}</pre>
          </div>
          {:else}
          <div>
            <h6 class="responsesstyles">There is no Response</h6>
          </div>
          {/if}
        </div>
      </div>
</div>
<!-- <FooterComponent/> -->