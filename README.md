[![Latest Stable Version](https://poser.pugx.org/bankiru/jsonrpc-server-bundle/v/stable)](https://packagist.org/packages/bankiru/jsonrpc-server-bundle) 
[![Total Downloads](https://poser.pugx.org/bankiru/jsonrpc-server-bundle/downloads)](https://packagist.org/packages/bankiru/jsonrpc-server-bundle) 
[![Latest Unstable Version](https://poser.pugx.org/bankiru/jsonrpc-server-bundle/v/unstable)](https://packagist.org/packages/bankiru/jsonrpc-server-bundle) 
[![License](https://poser.pugx.org/bankiru/jsonrpc-server-bundle/license)](https://packagist.org/packages/bankiru/jsonrpc-server-bundle)

[![Build Status](https://travis-ci.org/bankiru/jsonrpc-server-bundle.svg)](https://travis-ci.org/bankiru/jsonrpc-server-bundle)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/bankiru/jsonrpc-server-bundle/badges/quality-score.png)](https://scrutinizer-ci.com/g/bankiru/jsonrpc-server-bundle/)
[![Code Coverage](https://scrutinizer-ci.com/g/bankiru/jsonrpc-server-bundle/badges/coverage.png)](https://scrutinizer-ci.com/g/bankiru/jsonrpc-server-bundle/)
[![SensioLabsInsight](https://insight.sensiolabs.com/projects/8b87b0a7-c8f9-4d7b-9b91-1e4e762a686f/mini.png)](https://insight.sensiolabs.com/projects/8b87b0a7-c8f9-4d7b-9b91-1e4e762a686f)

# JSON-RPC Server bundle

This bundle provides JSON-RPC 2.0 server on top of the `bankiru/rpc-server-bundle` library.

## Serialization

This bundle uses `rpc.view` event in order to serialize response if request
was instance of `JsonRpcRequestInterface` and the response is not a 
`JsonRpcResponseInterface` object. You can bypass the serialization process 
with sending pre-created response object or implementing your own view listener

### `json_encode` serializer listener (Default)

Default serialization process uses `json_encode` function to convert the response data into string representation.
Implement `\JsonSerializable` to configure the structure of the response

### JMS Serializer listener (Optional)

This library can utilize [JMS Serializer Bundle](https://github.com/schmittjoh/JMSSerializerBundle) 
to automatically serialize non-JSON-RPC controller response to serialized view. 
You can specify the serialization context at the routing level. Also you 
could disable default context usage with `with_default_context: false`

Proceed to [official documentation](http://jmsyst.com/bundles/JMSSerializerBundle) for more information

## Exception handling

Any unhandled exception from the controller would be automatically 
converted to the proper JSON-RPC failure response with INTERNAL_ERROR (-32603) code.
If you want do display custom error you could extend `JsonRpcException` class or 
configure it manually like following:

```php
$exception = new JsonRpcException();
$execption->setJsonRpcError(
    new JsonRpcError(
        JsonRpcError::METHOD_NOT_FOUND,
        'Invalid method',
        (object)['debug_data' => 'some debug data']
    )
);
```

Some exceptions are handled in a more specific manner

* `AccessDeniedException` will result in `-32601` (method not found) json-rpc error code.
* `MethodNotFoundException` will result in `-32601` (method not found) json-rpc error code.
* `InvalidMethodParametersException` will result in `-32602` (invalid params) json-rpc error code

## Configuration

Configuration reference:

```yaml
jsonrpc_server:

    # View listener service ID
    view_listener:        jsonrpc_server.builtin_adapter.view_listener
    adapters:
        jms:
            relation_handlers:

                # Prototype: Key: Relation handler name (i.e. "Relation"), Value: service ID for relation handler entity manager
                handler:              ~
```

## Specification

Refer to official JSON-RPC 2.0 specification at http://www.jsonrpc.org/specification
