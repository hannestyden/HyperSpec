# This is Work in Progress

             __  __                     ______
            / / / /_  _________________/ ____/________________
           / /_/ / / / / __ \  _ \_ __/____ \/ __ \  _ \  ___/
          / __  / /_/ / /_/ /  __/ /  ____/ / /_/ /  __/ /__
         /_/ /_/\__, / ____/\___/_/  /_____/ ____/\___/\___/
               /____/_/                   /_/

A full stack testing framework for HTTP APIs.

By extending `minitest/spec` HyperSpec provides a Ruby DSL for testing
HTTP APIs from the outside.

## Example

    service "http://api.lolcat.biz" do
      resource "/lolz" do
        get do
          it { responds_with.status :ok }
          it { response.json['lolz].must be_an(Array) }

          with_query("q=monorail") do
            it "only lists lolz that match the query" do
              response.json['lolz'].each do |lol|
                lol['title'].must =~ /monorail/
              end
            end
          end
        end

        post do
          context "without request body" do
            it { responds_with.status :unprocessable_entity }
          end

          context "with request body" do
            context "in JSON format" do
              with_headers      { 'Content-Type' => 'application/json' }
              with_request_body { "title" => "Roflcopter!" }.to_json

              it { responds_with.status :created }
              it { response.json['lol']['title'].must == 'Roflcopter!' }
            end
          end
        end
      end
    end

## Concepts

### `service`

Sets the `BASE_URI` of the API.

### `resource`

Sets the `URI` under test. Absolute or relative to the current scope.

### `get`, `head`, `post`, `put` and `delete`

Selects the HTTP method for the request.

### `with_query_string`

Sets the query parameters used for a request. Merges with previously set parameters.

### `with_headers`

Sets the headers used for a request. Merges with previously set headers.

### `with_body`

Sets the body used for a request. Overrides previously set parameters.

### `response`

An object for accessing properties of the "raw" response.

### `responds_with`

Issues the request and returns an object that has the following convenience matchers:

#### `status`

Allows for comparing against named status code symbols.

#### `content_type`

#### `content_charset`

## Upcoming features

### Representations

- DSL for matching representations.

## Concerns

- Efficient ways of building up and verifying precondition state.
- Verify an eventual consistent state.
- Allowing whitebox testing by "wormholing" into the application(s).
- How to test a testing library? #meta

## Acknowledgments

Thanks to Daniel Bornkessel for inspiring me to do README driven development.
