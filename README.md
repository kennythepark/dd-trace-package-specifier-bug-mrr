# dd-trace-package-specifier-bug-mrr
Minimum reproduction repository for the `dd-trace` package specifier bug. This repo builds on top of `@nestjs/nest`'s sample repo `04-grpc`. 

# Steps To Replicate

1) npm i
2) npm run build
3) node --require dd-trace/init dist/main

# Comment

The `wrapPackageDefinition` function in `dd-trace/packages/datadog-plugin-grpc/src/client.js` assumes the basis of calling `wrapClientConstructor` on the existence of the `service` property, the assumption here is that the `ServiceClient` object instance holds the `service` property. This collides with package specifiers named as `X.service` as the resulting gRPC object will have the `service` package [as an "inner" to its parent package](https://developers.google.com/protocol-buffers/docs/proto3#services), which will erroneously be passed to the `wrapClientConstructor` function and ultimately fail with a type error as the [`proto` constant](https://github.com/DataDog/dd-trace-js/blob/master/packages/datadog-plugin-grpc/src/client.js#L60) will result as undefined.
