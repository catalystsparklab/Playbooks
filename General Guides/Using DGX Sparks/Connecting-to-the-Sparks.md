# How to connect to the Sparks

**This guide is for using the DGX Sparks as an inference engine. This does not cover model training/fine-tuning, SSH connections or other projects on the Sparks**

The DGX Sparks are accessible in the cyber lab only. In order to access them, you must be given a LiteLLM API key for yourself or your team. 

## Getting an API key

Ask Bitner for an API key for LiteLLM. Once you get it, you'll need to connect to the [endpoint]. You can do this in-browser or you can use a harness to connect to. For this guide we'll be referencing how to add it to a harness

## Connecting to the endpoint

You'll need to add the base URL (Insert here) to your harness of choice. The base URL is the LiteLLM endpoint. It is an OpenAI-API-Compatible endpoint that should be compatible with virtually any harness or agent you want to use. 