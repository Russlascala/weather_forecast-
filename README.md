# weather_forecast-
A weather forecast from zip code, using open weather map api, as well as google maps api.
<!DOCTYPE html>
<html lang="en">
	<head>
		<meta charset="UTF-8">
		<title>Weather App</title>
		<link rel="stylesheet" type="text/css" href="styles.css" />
		<meta name="viewport" content="width=device-width, initial-scale=1">
		<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
		<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
		<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
		<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
		<script src="https://maps.googleapis.com/maps/api/js?v=3.exp&sensor=true&libraries=places"></script>	
	</head>
	<nav class="navbar navbar-inverse">
		<div class="container-fluid">
			<div class="navbar-header">
				<a class="navbar-brand" href="#">Russ LaScala</a>
			</div>
			<ul class="nav navbar-nav">
				<li class="active"><a href="#"><i class="fa fa-home fa-fw" aria-hidden="true"></i>Home</a></li>
				<li><a href="#"><i class="fa fa-rss fa-fw" aria-hidden="true"></i>Blog</a></li>
				<li><a href="#"><i class="fa fa-briefcase fa-fw" aria-hidden="true"></i>Work</a></li>
				<li><a href="#"><i class="fa fa-address-book-o fa-fw" aria-hidden="true"></i>Contact</a></li>
			</ul>
		</div>
	</nav>
	<body>
		<h1>Weather Forcast</h1>
		<div class="row">
			<div class="col-sm-4">
				<div id ="zipForm"><p id="localFor">Local forecast by ZIP code </p><input type="number" class="textfield" placeholder="Enter ZIP code ..." id= "zipcode">
				<button  type="submit" id="goButton">Go</button></input> 
				</div>
			</div>
			<div class="col-sm-8">
				<div class="weather-app">
					<div class="left">
						<div class="temperature"><span id="temperature">0</span>&deg;</div> 
						<div class="location"><span id="location">Unknown</span></div>
						<div class="ctof"><input class="toggle" type="checkbox" id="check" /></div>
					</div>	
					<div class="right">
						<div class="top">
							<img id="icon" width="75px" src="imgs/codes/200.png" />
						</div>
						<div class="bottom">
							<div class="humidity">
								<img src="imgs/humidity.png" height="16px" />
								<span id="humidity">0</span>%
							</div>
								  
							<div class="wind">
								<span id="wind">0</span> mph <span id="direction">N</span>
							</div>
						</div>
					</div>
				</div>
			</div>
		</div> 
		<div class="row">
			<div class="col-sm-16">
				<!-- Google map -->
				<div id="map"></div>
			</div>
		</div>
		<footer>
	            <div class="col-md-12">

                <div class="footer-socials mb-5 flex-center">

                    <!--Facebook-->
                    <a class="icons-sm fb-ic"><i class="fa fa-facebook fa-lg white-text mr-md-4" id ="icon"> </i></a>
                    <!--Twitter-->
                    <a class="icons-sm tw-ic"><i class="fa fa-twitter fa-lg white-text mr-md-4" id ="icon"> </i></a>
                    <!--Google +-->
                    <a class="icons-sm gplus-ic"><i class="fa fa-google-plus fa-lg white-text mr-md-4" id ="icon"> </i></a>
                    <!--Linkedin-->
                    <a class="icons-sm li-ic"><i class="fa fa-linkedin fa-lg white-text mr-md-4" id ="icon"> </i></a>
                    <!--Instagram-->
                    <a class="icons-sm ins-ic"><i class="fa fa-instagram fa-lg white-text mr-md-4" id ="icon"> </i></a>
                </div>
            </div>
        <p>&copy; Russell LaScala 2017</p>
	</footer>
	<script>
    /*
Weather application 
Author: Russ 
Date:   12.3.17

Filename: script.js
*/
var APPID = "2bb05b913f6132d247b6c2504501d887";
var temp;
var loc;
var icon;
var humidity; 
var wind;
var direction;
//checkbox for celcius to fahrenheit
var degChecked = document.getElementById('check');

//validates zipcode
function updateByZip(zip){
	var zip = document.getElementById("zipcode").value;
	var isValid = /^[0-9]{5}(?:-[0-9]{4})?$/.test(zip);
	var url = "http://api.openweathermap.org/data/2.5/weather?" + "zip=" + zip + "&APPID=" + APPID;
	if (isValid){
		console.log(zip);
		sendRequest(url);
	}
    else {
		alert('Invalid ZIP code.');
	}
}

//on click of goButton runs update by zip 
document.getElementById("goButton").addEventListener("click", updateByZip, false);
//for google maps location
document.getElementById("goButton").addEventListener("click", codeAddress, false);

//triggers goButton when enter is hit runs update by zip above ^^
var input =
document.getElementById("zipcode");
   input.addEventListener("keyup", function(event) {
    event.preventDefault();
    if (event.keyCode == 13) {
        document.getElementById("goButton").click();
    }
});

//sends request to get weather 	
function sendRequest(url){	
	var xmlhttp = new XMLHttpRequest();
	xmlhttp.onreadystatechange = function() {
		if (xmlhttp.readyState === 4){
			if ( xmlhttp.status === 200){
			var data = JSON.parse(xmlhttp.responseText);
			var weather = {};
			// can get more of these on website like weather discription, check it for waves
			weather.icon = data.weather[0].id;
			weather.humidity = data.main.humidity;
			weather.wind = data.wind.speed;
			weather.direction = degreesToDirection(data.wind.deg);
			weather.loc = data.name;
			if (degChecked.checked) {
				weather.temp = K2C(data.main.temp);
			} else {
				weather.temp = K2F(data.main.temp);
			}
			update(weather);
			console.log(weather.temp);
			}else {
			alert('ERROR: You have entered an international Postal Code, \nWe are unable to submit your request.\nPlease try again with a valid US Postal Code');
			} 
		}
	};
	xmlhttp.open("GET", url, true);
	xmlhttp.send();
}

//converts degrees to direction
function degreesToDirection(deg){
	console.log(deg);
	if(deg>11.25 && deg<33.75){
		return "NNE";
	}else if (deg>33.75 && deg<56.25){
		return "ENE";
	}else if (deg>56.25 && deg<78.75){
		return "E";
	}else if (deg>78.75 && deg<101.25){
		return "ESE";
	}else if (deg>101.25 && deg<123.75){
		return "ESE";
	}else if (deg>123.75 && deg<146.25){
		return "SE";
	}else if (deg>146.25 && deg<168.75){
		return "SSE";
	}else if (deg>168.75 && deg<191.25){
		return "S";
	}else if (deg>191.25 && deg<213.75){
		return "SSW";
	}else if (deg>213.75 && deg<236.25){
		return "SW";
	}else if (deg>236.25 && deg<258.75){
		return "WSW";
	}else if (deg>258.75 && deg<281.25){
		return "W";
	}else if (deg>281.25 && deg<303.75){
		return "WNW";
	}else if (deg>303.75 && deg<326.25){
		return "NW";
	}else if (deg>326.25 && deg<348.75){
		return "NNW";
	}else{
		return "N"; 
	}
}

//converts kelvin to celcius 
function K2C(k){
	return Math.round(k - 273.15);
}
//converts kelvin to fahrenheit
function K2F(k){
	return Math.round(k*(9/5)-459.67);
}

function update(weather) {
	temp.innerHTML = weather.temp;
	loc.innerHTML = weather.loc;
	wind.innerHTML = weather.wind;
	direction.innerHTML = weather.direction;
	humidity.innerHTML = weather.humidity;
	icon.src = "imgs/codes/" + weather.icon + ".png";
	console.log(icon.src);
}
//google map widgit 
var geocoder;
var map;

//standard location for start of google maps
function initialize() {
  geocoder = new google.maps.Geocoder();
  var loca = new google.maps.LatLng(41.7475, -74.0872);
  map = new google.maps.Map(document.getElementById('map'), {
    mapTypeId: google.maps.MapTypeId.ROADMAP,
    center: loca,
    zoom: 8
  });
}

//The clearall function is called when a button is clicked
function clearall() {
  	if (marker > 1) {
    map.setMap(null);
	}
}



function codeAddress() {
  var address = document.getElementById("zipcode").value;
  geocoder.geocode({
    'address': address
  }, function(results, status) {
    if (status == google.maps.GeocoderStatus.OK) {	
		map.setCenter(results[0].geometry.location);
		var marker = new google.maps.Marker({
        map: map,
        position: results[0].geometry.location
      });	
		console.log(marker);
    } else {
      alert("Geocode was not successful for the following reason: " + status);
    }
  });
}

google.maps.event.addDomListener(window, 'load', initialize);


//get location and updates weather 
/*function showPosition(position) {
	updateByGeo(position.coords.latitude, position.coords.longitude);
}*/

window.onload = function () {
	temp = document.getElementById("temperature");
	loc = document.getElementById("location");
	humidity = document.getElementById("humidity");
	wind = document.getElementById("wind");
	direction = document.getElementById("direction");
	icon = document.getElementById("icon");
}


/*
------------------------
---------to do----------
	- add 5 day forcast
	- add weather map layers
	-add weather stations 
*/
  </script>
  </body>
</html>
