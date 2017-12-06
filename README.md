# REST API Diagram 

This repo attempts to model the sample [webapibook/issuetracker](https://github.com/webapibook/issuetracker) REST API. 

The `issuetracker` was chosen because it provides a simple example of a Web API that conforms to the contraints of REST.

## Goals

1. Accurately model resources and relations as a state machine diagram.
1. Create an API specification as the source of truth. 
1. Generate documentation from the API specification.
1. Generate a mock server from the API specification.
1. Generate the state machine diagram in step 1. from the API specification.
1. Create consumer-driven contracts that are validated by the API specification.
1. Generate a mock server from the consumer-driven contracts.

![diagram](http://www.plantuml.com/plantuml/proxy?idx=0&src=https://raw.githubusercontent.com/josephgardner/rest-design/master/diagram.puml?rev=3 "state machine")
