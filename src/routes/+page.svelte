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
    let APIURL = "";
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
    let GetParams = {}
    let paramkey = ""
    let paramvalue = ""
    let Postbodytext = ""
    let Alertbox = false;
    let warnbox = false;
    let BLURL = "https://apiserver-roan.vercel.app/"  // API Calling URL
    // @ts-ignore
    const changeMethod = (e) => {
        APIMethod = e.target.innerHTML
    }

    // validation for the JSON format
    const isValidJSON = obj => {
      try {
        JSON.parse(obj);
        return true;
      } catch (e) {
        return false;
      }
    };

    // Add the Key value pair from the POST Method
    const AddKeyValuePair = () => {
        APIBody[bodyKey] = bodyvalue
    }

    // Remove the Key value pair from the POST Method
    const RemoveKeyValuePair = () => {
        delete(APIBody[bodyKey]);
        APIBody = APIBody
    }

    // Add the Key value pair from the Headers
    const AddKeyValuePairHeader = () => {
      HeaderBody[HeaderKey] = Headeralue
    }

    // Remove the Key value pair from the Headers
    const RemoveKeyValuePairHeader = () => {
        delete(HeaderBody[HeaderKey]);
        HeaderBody = HeaderBody
    }
    
    // Add the Key value pair from the GET Method
    const AddKeyValuePairQueryParam = () => {
      GetParams[paramkey] = paramvalue
      changeParamValue(paramkey,paramvalue,"ADD")
    }

    // Remove the Key value pair from the GET Method
    const RemoveKeyValuePairQueryParam = () => {
        delete(GetParams[paramkey]);
        GetParams = GetParams
        changeParamValue(paramkey,paramvalue)
    }

    // Change in the Textarea for the POST
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

    // Clear button Logic
    const ClearFunction = () => {
      APIMethod = "GET";
      APIURL = "";
      ResponseHeaders = undefined;
      ResponseData = undefined;
      ErrorMessage = undefined;
      Loader = false;
      bodyKey = ""
      bodyvalue = ""
      APIBody = {}
      HeaderKey = ""
      Headeralue = ""
      HeaderBody = {}
      GetParams = {}
      paramkey = ""
      paramvalue = ""
      Postbodytext = ""
      Alertbox = false;
      warnbox = false;
    }

    // Send Button Logic
    const sendFunc = async () => {
      Loader=true;
      document.getElementById("pills-contact-tab").click()
      if(APIURL)
      {
        let checkURL = new URL(APIURL)
        //console.table(checkURL)
        if(checkURL.hostname == "localhost" || checkURL.hostname == "127.0.0.1")
        {
          await axios({
                url: APIURL,
                method: APIMethod,
                timeout: 20000000,
                data:APIBody,
                headers:HeaderBody
              })
                .then((response) => {
                  ResponseHeaders = undefined;
                  ErrorMessage = undefined;
                  ResponseData = JSON.stringify(response.data,null,3);
                })
                .catch((error) => {
                  ResponseData = undefined;
                  ResponseHeaders = undefined;
                  ErrorMessage = JSON.stringify(error,null,3)
                });
        }
        else
        {
          if(APIMethod == "GET"){
            try{
              let Body = {APIURL:APIURL,headers:HeaderBody}
              axios.post(BLURL+"GET_ENDPOINT",Body).
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
                    ErrorMessage = JSON.stringify(responses.data,null,3)
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
              let Body = {APIURL:APIURL,data:APIBody,Headers:HeaderBody}
              axios.post(BLURL+"POST_ENDPOINT",Body).
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
                    ErrorMessage = JSON.stringify(responses.data,null,3)
                  }
                }
              })
              .catch((err)=>{
                //console.error(err)
                ErrorMessage = err;
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
      }
      else{
        Alertbox = true;
      }
      Loader=false;
    }

    // Download the API documentation
    const DownLoadFunc = async () => {
      const response = await fetch('DownloadTempate.txt');
      let fileData = await response.text()
      fileData = fileData.replace("[docs]","API Documentation as on "+new Date().toLocaleString()) // Add the First heading and as on Date
      fileData = fileData.replace("[URL]",APIURL) // URL 
      fileData = fileData.replace("[method]",APIMethod) // METHOD of API :- GET,POST
      fileData = fileData.replace("[RequestHeaders]",JSON.stringify(HeaderBody,null,3)) // Request Headers

      // Request Body depending on GET and POST
      if(APIMethod == "GET")
      {
        fileData = fileData.replace("[RequestBody]",JSON.stringify(GetParams,null,3))
      }
      else if(APIMethod == "POST")
      {
        fileData = fileData.replace("[RequestBody]",JSON.stringify(APIBody,null,3))
      }
      
      // Response Headers
      if(isValidJSON(ResponseHeaders))
      {
        fileData = fileData.replace("[ResponseHeaders]",JSON.stringify(JSON.parse(ResponseHeaders),null,3))
      }
      else{
        fileData = fileData.replace("[ResponseHeaders]",JSON.stringify(ResponseHeaders,null,3))
      }

      // Response Body
      if(isValidJSON(ResponseData))
      {
        fileData = fileData.replace("[ResponseBody]",JSON.stringify(JSON.parse(ResponseData),null,3))
      }
      else{
        fileData = fileData.replace("[ResponseBody]",JSON.stringify(JSON.parse(ResponseData),null,3))
      }

      // Create a Text file Blob
      var blob = new Blob([fileData],  
                {type : "text/plain"});
      var url = window.URL || window.webkitURL;
      let link = url.createObjectURL(blob);
      let a = document.createElement("a");
      a.setAttribute("download", `APIDocs.txt`); // Set the Default File Name for the document
      a.setAttribute("href", link);
      document.body.appendChild(a);
      a.click();
      document.body.removeChild(a);
    }

    // Change in URL
    const changeURLFunc = () => {
      if(APIMethod == "GET"){
        let queryurl = new URL(APIURL)
        let params = new URLSearchParams(queryurl.search)
        GetParams = {}
        for(var key of params.keys()){
          GetParams[key] = params.get(key)
        }
      }
    }

    // change params value
    const changeParamValue = (key,value,flag = "") => {
      if(flag == "ADD")
      {
        GetParams[key] = value
      }
      let params = GetParams;
      // convert object to list -- to enable .map
      let data = Object.entries(params);
      // encode every parameter (unpack list into 2 variables)
      data = data.map(([k, v]) => `${encodeURIComponent(k)}=${encodeURIComponent(v)}`);
      // combine into string
      let query = data.join('&');
      let newURL = new URL(APIURL)
      newURL.search = query
      APIURL = newURL.href
    }
</script>
<HeaderComponent />
  {#if Alertbox}
  <div class="alert alert-danger alert-dismissible fade show" role="alert">
    <strong>Please enter the URL in Textfield!</strong>
    <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close" on:click={() => {Alertbox = false}}></button>
  </div>
  {/if}
  {#if warnbox}
  <div class="alert alert-warning alert-dismissible fade show" role="alert">
    <strong>Are you Sure? you want to reset</strong>
    <button class="btn btn-success" on:click={() => {ClearFunction();warnbox=false}}>Yes</button>
    <button class="btn btn-success" on:click={() => {warnbox = false}}>No</button>
    <!-- <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close" on:click={() => {Alertbox = false}}></button> -->
  </div>
  {/if}
<div class="container-fluid">
    <div class="input-group mb-3" style="padding-top:10px">
        <button class="btn btn-success fw-bold dropdown-toggle" type="button" data-bs-toggle="dropdown" aria-expanded="false">{APIMethod}</button>
        <ul class="dropdown-menu">
          <li><button class="dropdown-item fw-bold" on:click={changeMethod}>GET</button></li>
          <li><button class="dropdown-item fw-bold" on:click={changeMethod}>POST</button></li>
        </ul>
        <input type="text" class="form-control" placeholder="Enter URL..." bind:value={APIURL} on:change={changeURLFunc} aria-label="Text input with dropdown button">
        <button type="button" class="btn btn-outline-success" on:click={sendFunc}>SEND <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrow-right-circle-fill" viewBox="0 0 16 16">
          <path d="M8 0a8 8 0 1 1 0 16A8 8 0 0 1 8 0M4.5 7.5a.5.5 0 0 0 0 1h5.793l-2.147 2.146a.5.5 0 0 0 .708.708l3-3a.5.5 0 0 0 0-.708l-3-3a.5.5 0 1 0-.708.708L10.293 7.5z"/>
        </svg></button>
        <button class="btn btn-outline-success" on:click={() => {warnbox = true}}>Clear <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-x-circle-fill" viewBox="0 0 16 16">
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
        {#if ResponseData || ResponseHeaders}
        <li class="nav-item" role="presentation">
          <button class="btn btn-warning" type="button" on:click={DownLoadFunc}>Download</button>
        </li>
        {/if}
      </ul>
      <hr>

      <!-- Tab Contents -->
      <div class="tab-content" id="pills-tabContent">
        <!-- Headers -->
        <div class="tab-pane fade show active" id="pills-home" role="tabpanel" aria-labelledby="pills-home-tab" tabindex="0">
          <div>
            <div class="row">
              <div class="col">
                <input type="text" class="form-control" placeholder="Key" disabled={APIURL==""?true:false} bind:value={HeaderKey} aria-label="First name">
              </div>
              <div class="col">
                <input type="text" class="form-control" placeholder="Value" disabled={APIURL==""?true:false} bind:value={Headeralue} aria-label="Last name">
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
                    <input type="text" class="form-control" placeholder="Key" disabled={APIURL==""?true:false} bind:value={bodyKey} aria-label="First name">
                  </div>
                  <div class="col">
                    <input type="text" class="form-control" placeholder="Value" disabled={APIURL==""?true:false} bind:value={bodyvalue} aria-label="Last name">
                  </div>
                  <div class="col">
                    <button class="btn btn-success" on:click={AddKeyValuePair}>➕</button>
                    <button class="btn btn-success" on:click={RemoveKeyValuePair}>➖</button>
                  </div>
                </div>
                <br>
                <textarea class="form form-control" disabled={APIURL==""?true:false} cols="5" rows="6" on:change={ChangesPostBody} id="txtpostmeth">{JSON.stringify(APIBody,null,3)}</textarea>
                <hr>
                <div class="my-3">
                  <pre class="responsesstyles">{JSON.stringify(APIBody,null,3)}</pre>
                </div>
              </div>
              {:else}
              <div>
                <!-- <h6 class="responsesstyles">Only for the POST Request Body is accessable</h6> -->
                <!-- <pre class="responsesstyles">{JSON.stringify(GetParams,null,3)}</pre> -->
                <div class="row">
                  <div class="col">
                    <input type="text" class="form-control" placeholder="Param Key" disabled={APIURL==""?true:false} bind:value={paramkey} aria-label="First name">
                  </div>
                  <div class="col">
                    <input type="text" class="form-control" placeholder="Param Value" disabled={APIURL==""?true:false} bind:value={paramvalue} aria-label="Last name">
                  </div>
                  <div class="col">
                    <button class="btn btn-success" on:click={AddKeyValuePairQueryParam}>➕</button>
                    <button class="btn btn-success" on:click={RemoveKeyValuePairQueryParam}>➖</button>
                  </div>
                </div>
                <br>
                <div class="row">
                {#each Object.entries(GetParams) as [keypar,valuepar]}
                <div class="input-group mb-3">
                  <span class="input-group-text fw-bold" id="inputGroup-sizing-default">{keypar}</span>
                  <input type="text" class="form-control" aria-label="Sizing example input" aria-describedby="inputGroup-sizing-default" value={valuepar} on:input={(e) => changeParamValue(keypar,e.target.value,"ADD")}>
                </div>
                {/each}
              </div>
              </div>
            {/if}
        </div>
        </div>
        <!-- Response -->
        <div class="tab-pane fade" id="pills-contact" role="tabpanel" aria-labelledby="pills-contact-tab" tabindex="0">
          {#if ResponseData}
          <div>
            {#if ResponseHeaders}
            <h6>Response Headers <button class="btn btn-success" on:click={navigator.clipboard.writeText(ResponseHeaders)}>Copy <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-clipboard" viewBox="0 0 16 16">
              <path d="M4 1.5H3a2 2 0 0 0-2 2V14a2 2 0 0 0 2 2h10a2 2 0 0 0 2-2V3.5a2 2 0 0 0-2-2h-1v1h1a1 1 0 0 1 1 1V14a1 1 0 0 1-1 1H3a1 1 0 0 1-1-1V3.5a1 1 0 0 1 1-1h1z"/>
              <path d="M9.5 1a.5.5 0 0 1 .5.5v1a.5.5 0 0 1-.5.5h-3a.5.5 0 0 1-.5-.5v-1a.5.5 0 0 1 .5-.5zm-3-1A1.5 1.5 0 0 0 5 1.5v1A1.5 1.5 0 0 0 6.5 4h3A1.5 1.5 0 0 0 11 2.5v-1A1.5 1.5 0 0 0 9.5 0z"/>
            </svg></button></h6>
            <pre class="responsesstyles">{ResponseHeaders}</pre>
            <hr>
            {/if}
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