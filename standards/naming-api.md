# Microservice API Standars

This document provides guidelines for Microservice API formatting, observing some best practices for RESTful API interfaces.

Reference: https://github.com/WhiteHouse/api-standards

## General

After the name of the server the URL must contain the word "api".

Example: http://localhost/api

## Versions

After the word "api" the URL must have the version number, formed by the letter "v" and a sequential natural number.

Example: http://localhost/api/v1

## Microservice Root URI

Each Microservice Root URI must contain a short name of the Microservice in plural and it must return a swagger ouput with all available services.

Example: http://localhost/api/drivers -GET, must return a json answer with all services available.

Find out more: http://swagger.io

## HTTP Methods

The HTTP Methods supported should be:

* **GET** to retrieve information. Example:
http://localhost/api/drivers -GET

* **POST** to create registries. Example:
http://localhost/api/drivers -POST

* **PUT** to update registries. Example:
http://localhost/api/drivers -PUT

* **DELETE** to remove or inactivate registries. Example:
http://localhost/api/drivers/[id] -DELETE

## Authors

* Juan Sotomayor

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details


