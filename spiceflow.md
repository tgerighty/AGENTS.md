## calling the server from the client

for simple routes that only have one interaction in the page, for example a form page, you should use react-router forms and actions to interact with the server.

but when you do interactions from a component that can be rendered from multiple routes, or simply is not implemented inside a route page, you should use spiceflow client instead.

the website exposes an API via Spiceflow. here is spiceflow docs: https://getspiceflow.com/

> ALWAYS use the fetch tool to get the latest docs if you need to implement a new route in a Spiceflow API app server or need to add a new rpc call with a spiceflow api client!

Spiceflow has support for client side type safe rpc, use this client when you need to interact with the server from the client, for example for a settings save deep inside a component. here is example usage of it

> SUPER IMPORTANT! if you add a new route to a spiceflow app, use the spiceflow app state like `userId` to add authorization to the route. If there is no state then you can use functions like `getSession({request})` or similar.
> Make sure the current userId has access to the fetched or updated rows. This can be done by checking that the parent row or current row has a relation with the current userId. For example `prisma.site.findFirst({where: {users: {some: {userId }}}})`

Always use `const {data, error} = await apiClient...` when calling spiceflow rpc. If data is already declared, give it a different name with `const {data: data2, error} = await apiClient...` This patten of destructuring is preferred over for all apis that return data and error object fields.

## getting spiceflow docs

spiceflow is a little known api framework. If you add server routes to a file that includes spiceflow in the name or you are using the apiClient rpc you always need to fetch the Spiceflow docs first, using the @fetch tool on https://getspiceflow.com/

This url returns a single long documentation that covers your use case, always fetch this document so you know how to use Spiceflow. Spiceflow is different than hono and other api frameworks, that's why you should ALWAYS fetch the docs first before using it