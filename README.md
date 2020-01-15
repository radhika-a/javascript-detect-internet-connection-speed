# javascript-detect-internet-connection-speed
Ways to detecting internet connection speed and type of connection

### based on DOM loading time
add a timelog before loading the DOM (in head tag)
    
    window.DOMLoadingstartTime = new Date().getTime(); // add this in head tag it will return in milliseconds
Add another time log or check point after loading the DOM or in your component

    window.loaddiff = new Date().getTime() - starttime; //lets calculate time log based on initial time log
    let optimalTimeDiff = 2500;
    if(loaddiff < 2500){
      //logic to append video or ...
    }
    
### check the navigator.connection (experimental feature - not fully support by all browsers)

    navigator.connection
      //it will return below details as a object
        onchange: null
        effectiveType: "4g"
        rtt: 100
        downlink: 10
        saveData: false
        
effectivetype will tell the information about network connection type either it is a 4g connection/ 2g connection or 3g connection
downlink have the information download speed lower number is the lower speed. above log showing the 4g connection details here you can see downlink as 10 if you run the navigator.connection.downlink in 2g you can see near to 0.4, and it will be near to 1.5 in 3g connection.

    var connection = navigator.connection || navigator.webkitConnection || navigator.mozConnection;
    if(connection && (connection.effectiveType === "4g" || connection.effectiveType === "3g")){
       //lets include video in banner
    }else{
      //keep the same jpg/png image as banner
    }
    
But it is having the demit of browser compatibility. So, we can't really use this for remaining browsers. :(

### calculate the download speed based on downloading size of any asset. It is more accurate But we need to trigger a request of known asset.

    var assetUrl = "./image.jpg"; 
    var assetSize = 5032; //write in bytes  
    var desiredSpeedinMbps = 1;

    function InitiateSpeedDetection() {
        window.setTimeout(MeasureConnectionSpeed, 1);
    };    
    window.addEventListener('load', InitiateSpeedDetection, false);

    function MeasureConnectionSpeed() {
        var downloadBegainTime, downloadFinishTime;
        var downloadAsset = new Image();
        downloadAsset.onload = function () {
            downloadFinishTime = (new Date()).getTime();
            
            var downloadedTime = (downloadFinishTime - downloadBegainTime) / 1000;
            var actualFilesizeinBits = assetSize * 8;
            var speedinBps = (actualFilesizeinBits / downloadedTime).toFixed(2);
            var speedinKbps = (speedinBps / 1024).toFixed(2);
            var speedinMbps = (speedinKbps / 1024).toFixed(2);
            
            //lets check the 
            if(speedinMbps > desiredSpeedinMbps){
                console.log("connection speed is greater than 1mbps lets play video");
                // lets append video to banner
            }else{
                console.log("connection speed is lesser than 1mbps lets skip video");
                // lets keep showing the banner image don't replace any thing... 
            }
        }
        
        downloadAsset.onerror = function (err, msg) {
            // there is an error in downloading the asset, lets add fallback condition here
        }
        
        downloadBegainTime = (new Date()).getTime();
        downloadAsset.src = assetUrl + "?q=" + downloadBegainTime;
    }
     
    

