# Lesson 6: WeatherPy #

## Table of contents
 * [Initializing](#Initializing)
 * [Requesting Data](#Requesting-Data)
 * [Creating DataFrame](#Creating-DataFrame)
 * [Creating Data Visualization](#Creating-Data-Visualization)
 * [Creating Data Visualization](#Creating-Data-Visualization)

For this challenge, we are going to build a python script, using `jupyter notebook` and `Matplotlib`, to analyse the weather data from various cities and create visualizations. We will first get the cities using [citipy library](https://pypi.python.org/pypi/citipy) and then get the weather from [OpenWeatherMap API](https://openweathermap.org/api) in a `JSON` format.
 
## Initializing

* We we will be using the `starter_code` provided. Inside this folder we need to create a folder `output_data`, to store all the outputs. 
* Since we are not too far off what we have used so far, we can skip creating a new conda environment and activate our current conda environment. Followed by installing the [citipy] libary. Keep in mind, generally speaking it's good practice to create a new anaconda environment to install the depencies we will be using. 
  * `source activate [MyPythonDataEnv]` and then `pip install citipy` will do the trick.
    **or**
  * `source activate [MyPythonDataEnv] && pip install citipy`
* We then need to replace the content of [api_keys.py] with our own API Key from[OpenWeatherMap].
    1) [sign up](https://home.openweathermap.org/users/sign_up).
    2) get the API Key from the email sent out(check spam box).
      **if** you already have an account with `OpenWeatherMap` [navigate to this link](https://home.openweathermap.org/api_keys) and login.
    3) update the content of [api_keys.py] with our own API Key.

* Our `starter_code` already imports the depencies we will be using, including our `api_key` and `output path`. It even gets the cities we need the weather from. This means we can focus on the important parts. We should spend sometime getting familiar with the [OpenWeatherMap API Documentation](https://openweathermap.org/current). Once you familiarize yourself with the documentation start `jupyter notebook` from `terminal` or `git bash`.


## Requesting Data

* First, lets build our `url` to make `requests` to the `API`. In order to make a request, we need the `url` API point`http://api.openweathermap.org/data/2.5/weather?`, the parameters: `q={ city_name }`, `units={ units_format }` and `APPID={ api_key }`.We found this from the [OpenWeatherMap Documentation]("https://openweathermap.org/current"):
  * `url = "http://api.openweathermap.org/data/2.5/weather?units={ units_format }&APPID={ api_key }&q={ city_name }"`
  * Each of these values in our url will be replaced by a variable.
    * for example: `units_format = "imperial"` to get the temperature in fahrenheit.
* You have probably guessed the next step. For each element in the cities array we want to make a request to get the weather. And yes, you are right, we will append the results to a data list. However, we should also Keep in mind:
  1) We need the `JSON` response from the results. Using the `.json()` method on the response will do the trick.
  2) We should clean the data, getting only the information we need from the result. [OpenWeatherMap API Example Response](https://samples.openweathermap.org/data/2.5/forecast?q=M%C3%BCnchen,DE&appid=b6907d289e10d714a6e88b30761fae22) can serve as a guide.
  3) We do not control the results we are getting, thus we need to create our code robust in order to handle these errors.
   
**PRO TIP:**
  * We can print/pprint the response, it's not the case for this example but, the sources documentation can be outdated.
  * We should handle each possible exception separately.
    * Using the `raise_for_status` method to raise for any `HTTPError`. Let's make sure to import `from requests.exceptions import HTTPError` with the rest of our import statements.
    * `KeyError` for any data missing from the request.
  * We can use tools like [Postman](https://www.getpostman.com/) to test the api and the results.

```python
# Unit format
unit_format = "imperial"
# API point, the { city_name } parameter will added for each city 
url = "http://api.openweathermap.org/data/2.5/weather?units=" + unit_format + "&APPID=" + api_key + "&q=" 
# Results array
city_data = []

print("----------------");
print("Fetch Data Begin");
print("----------------");

# Loop through all the cities in our list
for city in cities:
    print("Processing request for " + city)
    try:
        # Create a request to the API point
        res = requests.get(url+city);

        # Raise an HTTPError if recieved an error status
        res.raise_for_status();

        # Parse the JSON data
        result = res.json();

        # Append the City information into city_data list
        city_data.append({  "City": city,
                            "Lat": result["coord"]["lat"], 
                            "Lng": result["coord"]["lon"], 
                            "Max Temp": result["main"]["temp_max"],
                            "Humidity": result["main"]["humidity"],
                            "Cloudiness": result["clouds"]["all"],
                            "Wind Speed": result["wind"]["speed"],
                            "Country": result["sys"]["country"],
                            "Date": result["dt"] })

    # Handle status coder error
    except HTTPError:
      print("Response status code error. Skipping city...")
      pass

    # Handle error if missing data
    except KeyError:
      print("Data does not have sufficient information. Skipping city...")
      pass
    else:
      print("Fetched the data.")
print("-------------------");
print("Fetch Data Complete");
print("-------------------");
```
## Creating DataFrame

* In order to analyze our data and export it, we will need to create a `DataFrame`. Pandas can parse `JSON` file and make a DataFrame right from it.
* Exporting the file will be just as easy with the `to_csv` function we have used before. We can use the variable `output_data_file` pre-defined for the output. 
* We want to also extract any of the data we will be comparing:
  1)  Latitude
  2)  Max Temperature
  3)  Humidity
  4)  Cloudiness
  5)  Wind Speed
   
**PRO TIP:** 
  * Print the data to ensure we are getting the expected results.

```python
# Convert array of JSONs into Pandas DataFrame
city_data_pd = pd.DataFrame(city_data)

# Export the results from the API into a csv file
city_data_pd.to_csv(output_data_file, index_label="City_ID")

# Extract relevant fields from the DataFrame
lats = city_data_pd["Lat"]
max_temps = city_data_pd["Max Temp"]
humidity = city_data_pd["Humidity"]
cloudiness = city_data_pd["Cloudiness"]
wind_speed = city_data_pd["Wind Speed"]

# Show Record Count
city_data_pd.count()

# Display the City Data Frame
city_data_pd.head()
```

## Creating Data Visualization

* We can now create the scatter plots for the data analysis using `Matplotlib.scatter()`. The altitude stays consistent and will be compared against ` max_temps, humidity, cloudiness and wind_speed`.
  * Example scatter plot: `plt.scatter(x, y, edgecolor, linewidths, marker, alpha, label)`.
* We must also add the `title, ylabel and xlabel`.
* Additionally, output the scatter plot to a `png` image file.
* Finally, display the scatter plot for our analisys. 

**PRO TIP:**
  * Draw a grid on the scatter plot, will help make it more readable.

#### Temperature (F) vs. Latitude
```python
# Build scatter plot for latitude vs. Temperature
plt.scatter(lats, 
            max_temps,
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Cities")

# Incorporate the other graph properties
plt.title("City Latitude vs. Max Temperature (%s)" % time.strftime("%x"))
plt.ylabel("Max Temperature (F)")
plt.xlabel("Latitude")
plt.grid(True)

# Save the figure
plt.savefig("output_data/Fig1.png")

# Show plot
plt.show()
```

#### Humidity (%) vs. Latitude

```python
# Build the scatter plots for latitude vs. humidity
plt.scatter(lats, 
            humidity,
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Cities")

# Incorporate the other graph properties
plt.title("City Latitude vs. Humidity (%s)" % time.strftime("%x"))
plt.ylabel("Humidity (%)")
plt.xlabel("Latitude")
plt.grid(True)

# Save the figure
plt.savefig("output_data/Fig2.png")

# Show plot
plt.show()
```

#### Cloudiness (%) vs. Latitude
```python
# Build the scatter plots for latitude vs. cloudiness
plt.scatter(lats, 
            cloudiness,
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Cities")

# Incorporate the other graph properties
plt.title("City Latitude vs. Cloudiness (%s)" % time.strftime("%x"))
plt.ylabel("Cloudiness (%)")
plt.xlabel("Latitude")
plt.grid(True)

# Save the figure
plt.savefig("output_data/Fig3.png")

# Show plot
plt.show()
```
#### Wind Speed (mph) vs. Latitude
```python
# Build the scatter plots for latitude vs. wind speed
plt.scatter(lats, 
            wind_speed,
            edgecolor="black", linewidths=1, marker="o", 
            alpha=0.8, label="Cities")

# Incorporate the other graph properties
plt.title("City Latitude vs. Wind Speed (%s)" % time.strftime("%x"))
plt.ylabel("Wind Speed (mph)")
plt.xlabel("Latitude")
plt.grid(True)

# Save the figure
plt.savefig("output_data/Fig4.png")

# Show plot
plt.show()
```
## Data Analysis

* For your final step, analyze your data for each scatter plot to find any correlation.

#### Temperature (F) vs. Latitude
* The concentration is between 60(F) to around 85(F) around the equator. Between latitude -20 to 20. The Temperature increases as we get closer to the equator and then decreases as we move away from the equator, creating a upside down parabola. Consequently, weather is warmer around the equator.

![Temperature (F) vs. Latitude](./Solved/output_data/Fig1.png)

#### Humidity (%) vs. Latitude
* The concentration is between 80% to 100%, irrespectively of the latitude. Consequently, latitude doesn't affect humidity.

![Humidity (%) vs. Latitude](./Solved/output_data/Fig2.png)

#### Cloudiness (%) vs. Latitude
* The concentration seems to be around 0% or around 80%, mostly scattered around the plot, irrespectively of the latitude. Consenquently, latitude does not affect Cloudiness.

![Cloudiness (%) vs. Latitude](./Solved/output_data/Fig3.png)

#### Wind Speed (mph) vs. Latitude
* The concentration seems to be around 0mph to 10mph, irrespectively of the latitude. Consenquently, latitude does not affect Cloudiness.

![Wind Speed (mph) vs. Latitude](./Solved/output_data/Fig4.png)


##  FAQ
  * What is an API? [wikipedia](https://en.wikipedia.org/wiki/Application_programming_interface)
  * What is JSON? [w3schools](https://www.w3schools.com/whatis/whatis_json.asp)

