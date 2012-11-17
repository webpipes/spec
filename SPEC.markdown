# WebPipes

## Terms:

- block: a web service API with machine readable description
- trigger: a block with a well defined API to register an HTTP callback for invoking an action
- action: a block that performs some action and has no outputs
- pipeline: a description of how multiple blocks inputs and outputs are connected
- pipeline editor: UI for composing a pipeline, e.x. Yahoo Pipes, IFTTT, etc
- pipeline executor: potentially a block that executes a pipeline
- composite block / pipeline block: a pipeline executor with pipeline description
- composite trigger: a pipeline block used as a trigger
- composite action: a pipeline block used as an action


## WebPipe Definition

	{
	    name: String
	    description: String
	    inputs: {
	        name: String
	        type: String (Array, String, Number, Boolean)
	        description: String
	        optional?: Boolean
	        default?: Any
	    }
	    outputs: {
	        name: String
	        type: String (Array, String, Number, Boolean)
	        description: String
	    }
	}
    

## Invoking/Using a Block

POST http://block-endpoint
    => JSON object of input data
    <= JSON object/array of output data
OPTIONS http://block-endpoint
    <= JSON object of block def

http://zacstewart.com/2012/04/14/http-options-method.html

Triggers use tentative HTTP Subscriptions spec or some simple variation.


## Pipeline Defintion 

	{
	    name: String
	    description: String
	    blocks: [
	        {
	            id: Number
	            url: String
	        }
	    ]
	    pipes: [
	        {
	            source_block: Number
	            source_output: String
	            target_block: Number
	            target_input: String
	        }
	    ]
	    inputs: {
        
	    }
	    outputs: {
        
	    }
	}


## Things to Prototype

- example triggers/blocks/actions
- pipeline executor
- pipeline editor
- block client library, server framework/helper libs