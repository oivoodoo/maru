# Maru

> Elixir copy of [grape](http://intridea.github.io/grape/) for creating REST-like APIs.

[![Build Status](https://api.travis-ci.org/falood/maru.svg)](https://travis-ci.org/falood/maru/)
[![hex.pm Version](https://img.shields.io/hexpm/v/maru.svg)](https://hex.pm/packages/maru)

## Usage

```elixir
defmodule Router.User do
  use Maru.Router

  namespace :user do
    route_param :id do
      get do
        %{ user: params[:id] } |> json
      end

      desc "description"
      params do
        requires :age,    type: Integer, values: 18..65
        requires :sex,    type: Atom, values: [:male, :female], default: :female
        group    :name,   type: Map do
          requires :first_name
          requires :last_name
        end
        optional :intro,  type: String, regexp: ~r/^[a-z]+$/
        optional :avatar, type: File
        optional :avatar_url, type: String
        exactly_one_of [:avatar, :avatar_url]
      end
      post do
        ...
      end
    end
  end
end

defmodule Router.Homepage do
  use Maru.Router

  resources do
    get do
      %{ hello: :world } |> json
    end

    mount Router.User
  end
end


defmodule MyAPP.API do
  use Maru.Router

  plug Plug.Static, at: "/static", from: "/my/static/path/"
  mount Router.Homepage

  def error(conn, _e) do
    "Server Error" |> text(500)
  end
end
```

then add the `maru` to your `config/config.exs`
```elixir
config :maru, MyAPP.API,
  port: 8880
```

For more info, you can move to [Getting Started Guide](https://github.com/falood/maru/blob/master/guide/getting_started.md) and [Router Guide](https://github.com/falood/maru/blob/master/guide/router.md)

## TODO

- [X] params DSL
- [X] `mutually_exclusive` `exactly_one_of` `at_least_one_of` DSL for params
- [X] group DSL for params
- [X] header DSL
- [X] assign DSL
- [X] helper DSL
- [X] generate docs
- [X] custom params validators
- [ ] version support
- [ ] generate detail docs include params and version
- [ ] https support
