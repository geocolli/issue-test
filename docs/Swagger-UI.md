# Harmony: Swagger UI

**Note**: at this time, we have decided not to check in swagger-ui in cx code base itself. This may change after the open source license review. So for time being, you need to install swagger UI elsewhere, and point it to your CX server.

*   Clone [https://github.com/swagger-api/swagger-ui](https://github.com/swagger-api/swagger-ui)
*   git checkout v2.2.10 (**this step is important, since latest swagger-ui doesn't have support for swagger API version 1.2**)
*   cd dist;
*   Edit index.html, change url variable to point to [https://wfmIP:wfmPort/apidocs/swagger.json](https://wfmIPwfmPort), e.g. [https://192.168.99.100:31997/apidocs/swagger.json](https://192.168.99.100:31997/apidocs/swagger.json) if you are using minikube
*   open index.html in browser, you should see latest API docs.
