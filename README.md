# Lesson 6: WeatherPy #

For this challenge, we are going to build a python script, using [jupyter-notebook] and [Matplotlib], to analyse the weather data from various cities and create visualisations. We will first get the cities using [citipy library](https://pypi.python.org/pypi/citipy) and then get the weather from [OpenWeatherMap API](https://openweathermap.org/api) in a [JSON] format.
 
## Initializing

* Since we are not too far off what we have used so far, we can skip creating a new conda environment and activate our current conda env. Follow by installing the [citipy] libary. Keep in mind, generally speaking it's good practice to create a new anaconda environment to install the depencies we will be using. 
  * `source activate [MyPythonDataEnv]` and then `pip install citipy` will do the trick, *or*
  * `source activate [MyPythonDataEnv] && pip install citipy`
* We then need to replace the content of [api_keys.py] with our own API Key from[OpenWeatherMap].
    1) [sign up](https://home.openweathermap.org/users/sign_up).
    2) get the API Key from the email sent out(check spam box). *if* you already have an account [OpenWeatherMap]("https://home.openweathermap.org/api_keys") and login.
    3) update the content of [api_keys.py] with our own API Key.

* Our [Starter_code] already imports the depencies we will be using, including our [api_key]. It even gets the cities we need the weather from. This means we can focus on the important parts. But, we should spend sometime getting familiar with the [OpenWeatherMap API Documentation]("https://openweathermap.org/current").



##  FAQ
    What is an API?
    What is an API Key?
    What is JSON?

