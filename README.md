# fauna-test

## Assumsptions
  * The front end component(s) will be embedded in some other page at some point
  * Styling/design will be provided later

## Next Steps
  * Figure out if there's a way to resolve references on the Fauna DB side instead of the client side, to reduce the number of queries made
  * Enhance models to match retrieval of data, do one of these:
    * add possibility that a field is actually a reference)
    * enhance retrieval to resolve *all* references and match models
  * Did I miss entity ID somewhere? those would've been nice to have on the frontend when displaying orders
