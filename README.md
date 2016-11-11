# JsonApiResponders

[![Gem Version](https://badge.fury.io/rb/json_api_responders.svg)](https://badge.fury.io/rb/json_api_responders)
[![Build Status](https://semaphoreci.com/api/v1/infinum/json_api_responders/branches/features-missing_responses/shields_badge.svg)](https://semaphoreci.com/infinum/json_api_responders)
[![Code Climate](https://codeclimate.com/github/infinum/json_api_responders/badges/gpa.svg)](https://codeclimate.com/github/infinum/json_api_responders)
[![Test Coverage](https://codeclimate.com/github/infinum/json_api_responders/badges/coverage.svg)](https://codeclimate.com/github/infinum/json_api_responders/coverage)

This gem gives a few convenient methods for working with JSONAPI. It is inspired by responders gem.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'json_api_responders'
```

And then execute:

    $ bundle

## Usage
This gem comes with the two following methods `respond_with` and `respond_with_error`.

##### `respond_with(resource, options = {}) `
This method requires a resource as a parameter, and you can pass some options if you wish. Any options you do choose to pass into `respond_with` will be passed on to the `controller.render` method. In the [Configuration section](#configuration) you can learn how to set mandatory options. Bellow you will find a few examples on how to use this method:

    user = User.first
    respond_with user

The above example will render the **User** object.

    user = User.first
    respond_with user, on_error: { status: :unauthorized, detail: 'Invalid user or password' }

The above example will render an **Error** response if an error would occur.

##### `respond_with_error(status, detail = nil)`
This method requires HTTP status code and an optional parameter explaining the error. This method will render an error message as described in the JSON API specification. Below you can see an example of how it should be used:

    respond_with_error, status: 401, detail: 'Bad credentials'
    respond_with_error, status: 404, detail: 'Not found'
    respond_with_error, status: 400, detail: 'Bad request'


## Configuration
Currently you can only configure which options are required to be passed through the `respond_with` method. These required options are categorized by the controller's actions. Bellow you can find an example:

    JsonApiResponders.configure do |config|
        config.required_options = {
          index: [:each_serializer],
          create: [:serializer]
        }
    end
    ...
    def create
      user = User.create(...)
      respond_with user, serializer: UserSerializer
    end

If `:serializer` was left out of the above `respond_with` method you would see the `JsonApiResponders::Errors::RequiredOptionMissingError` be raised.

## Responses

### index

    render json: resource, status: 200

### show

    render json: resource, status: 200

### create

    if resource.valid?
      render json: resource, status: 201
    else
      render error: errors, status: 409

### update

    if resource.valid?
      render json: resource, status: 200
    else
      render error: errors, status: 409

### destroy

    head status: 204

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/infinum/json_api_responders.
