# README

Welcome to getting setup with local AI! In this repo you'll find multiple guides for getting setup with using local models, harness deployment, and more. **We've designed this to be used by beginners and advanced users!** 

## Getting started

Preparing and setting up AI to run on your machine can be very useful. Here's some quick benefits to using AI locally:
- **No subscription/API costs**: When you run models on your machine, the only cost is the electricity needed to run the machine.
- **"Unlimited" usage... No need to reset your usage cap!**: You can use a local model as much as you want. **The only catch** is how much *context* your model can run, as well as how "big" of a model you can run. This will be highly dependent on the model you run and the size of your context window that you use.
- **No data uploaded to the cloud**: Anything you give the model stays on your machine unless you have it upload something to the internet. That means your chats, files, and other content stay on your machine when using the model.

This guide is intended to be OS-agnostic and will help you get setup with the basics for running local AI models, as well as agents. 

### Prequisites
**It is strongly recommended** that you understand the following before using a local model:
- **You must have a dedicated GPU for optimal performance** (Ideally an NVIDIA GPU, but AMD works as well.) A GPU with 12GB+ of VRAM is better for running larger models with more context. *If you are on a Mac* it is ideal if you have an M-series processor with 24-32GB of unified memory. 
- Know the basics of what *context* and *context windows* are. 
- Know the basics of what tokenization is.
- What a model vs. an agent is.

Having a way to interact with the model is also very useful. By default, you can only chat with a model when running it. It cannot search the internet, read/edit files, or use tools. Using a *harness* or agent runtime changes this and allows you to give the model "eyes" and "hands" to start interacting with your computer. This guide will also walk you through the basics of how to do this. 

### "Just tell me how to run a model!" 
Go to `Beginner.md` to get started if you've never setup a local model and agent before.


## Additional resources

You can find additional resources in the Context Library's Docs folder.