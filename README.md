# fauna-test

## Assumptions
  * The front end component(s) will be embedded in some other page at some point
  * Styling/design will be provided later

## Notable Dependencies
  * [express](https://expressjs.com/) to handle API requests and serve static frontend files
  * [react](https://reactjs.org/) a frontend javascript framework
  * [material-ui](https://material-ui.com/) for decent looking, easy to use, front end components


## Setup, Demonstration, Testing

### Deployed
The project is deployed to my AWS account right now. You should be able to load the page at this URL: [https://09mnthpgkd.execute-api.us-east-1.amazonaws.com/dev/fauna-test/](https://09mnthpgkd.execute-api.us-east-1.amazonaws.com/dev/fauna-test/). It's all hosted from a Lambda though, so be patient. The first load may take 10+ seconds, but subsequent loads/reloads should be fairly quick.

### Dependencies
The project depends on Node.js and NPM. You can find out more about how to install those [here](https://www.npmjs.com/get-npm). Once that's taken care of, ideally, all project dependencies should be installed by running the following command:
```bash
npm install
```

### Local testing
The above command will install dependencies for both frontend and backend projects. Once that's done, there's a single command to build both projects, copy the frontend into the backend's folder, then start the backend. This simulates what is actually happening when deployed.
```
npm run start-local
```

Once they've completed, you should be able to access the backend `order-summary` endpoint [here](http://localhost:8080/fauna-test/order-summary). The frontend should be available [here](http://localhost:8080/fauna-test/). You should see something like this:
![frontend screenshot](frontend-screenshot.png)

### Unit testing
These two commands will execute unit tests for each project:
```
npm run test-backend
npm run test-frontend
```

This command will generate backend code coverage reports:
```
npm run test-coverage-backend
```

## Decisions/Thought Process
### Backend
  * I built the backend with the intention of deploying it to AWS in a lambda. It's what I'm used to these days and that's the lowest (personal) effort for me to get it deployed and viewable somewhere. That's why there are a couple of extra files and some dependencies on AWS libraries.
  * I built it first so I could use it for testing. That's why there are some extra endpoints to retrieve raw customer, order, and product data as well as Handler classes to handle calls to those endpoints. Those need to be dropped before the service is actually used. 
  * I tried to build interfaces (really just abstract classes, so I could be more strict with their properties) mostly to show that I knew how, but also to make switching to some other DB (or switching to GraphQL) as easy as writing a new extension of `StorageUtil`.
  * I modeled most of the data at rest, but balked at fully modeling all response types when I saw the complicated `@ref` attributes. It would be nice to have those, but it would be REALLY nice if the faunadb sdk could type the shape of its return values with something a little better than `object`.
  * I sank a decent amount of time into trying to figure out if a query could resolve all references instead of storing them in `@ref` fields. I didn't find anything before I decided to cut bait and make separate calls to resolve all references.
  * I set test coverage requirements to 95% as there are some files with only `interface`s in them, which `ts-jest` doesn't record as being covered. They could have some stuff added, like maybe a `namespace` object with some kind of "create" method in it to verify all attributes are present at runtime, but that's probably way out of scope for this, and definitely not worth doing for that extra 5% of coverage.

### Frontend
  * I'm not so great at making full pages that look sharp, so I stuck the two frontend Assumptions at the top of this README. I feel like that's a pretty valid assumption since functionality this small is likely to be embedded in some larger page and would probably need to be restyled to fit its look.
  * I tried to keep things as modular as possible without going overboard. `LineItemTable` could probably use its own file, but sticking it at the top of `OrderSummaryCard` seemed to make more sense at the time.
  * I saved frontend unit tests for last, but I probably shouldn't have. I ran out of energy after struggling a little with deployment. You can see some examples of front end tests I've written in my [local-emailer](https://github.com/champgm/local-emailer/blob/React-Native/spec/Emailer.spec.tsx) project though. 

### Deployment
  * Probably 80% of the API Gateway deployment resource is not needed. It's from a personal project where I stored the frontend in S3 and the backend API was in a Lambda; most of that is set up so CORS will work. For this project, it would take too much time to re-learn how it all works and pick out the unneeded stuff. 
  * Getting react to work properly inside of a Lambda and locally for testing was a little weird, but surprisingly easy.


## Next Steps
  * Finish front end unit tests
  * Figure out if there's a way to resolve references on the Fauna DB side instead of the client side, to reduce the number of queries made
  * Enhance models to match retrieval of data, do one of these:
    * add possibility that a field is actually a reference
    * enhance retrieval to resolve *all* references and match models
  * Did I miss entity ID somewhere? those would've been nice to have on the frontend when displaying orders
  * Filters/sorting on the customer list would be nice, but definitely out of scope. Right now it's sorted by total spent, descending