# raven

Raven is a CLI app written in Elixir to display GitHub issues for a particular project.

### Background

This project is part of [52projects](https://donny.github.io/52projects/) and the new stuff that I learn through this project: the [Elixir](http://elixir-lang.org) programming language.

### Elixir Language

[Elixir](http://elixir-lang.org) is a dynamic, functional language that runs on the Erlang VM (BEAM). According to [Wikipedia](https://en.wikipedia.org/wiki/Elixir_(programming_language)), it was created to enable higher extensibility and productivity in the Erlang VM while keeping compatibility with Erlang's ecosystem. Like Erlang, all Elixir code runs inside lightweight threads of execution (called processes). They are isolated and exchange information via messages.

Elixir comes with a great tool called [Mix](https://hexdocs.pm/mix/Mix.html) to simplify development. It is a build tool that allows you to create projects, manage tasks, and manage dependencies. Mix integrates nicely with the [Hex package manager](https://hex.pm), which provides dependency resolution and the ability to remotely fetch packages. A small example of using Mix:

```shell
$ mix new my_app
$ cd my_app
$ mix test
.

Finished in 0.04 seconds (0.04s on load, 0.00s on tests)
1 tests, 0 failures
```

Elixir also comes with an interactive shell called [IEx](https://hexdocs.pm/iex/IEx.html) to quickly interact with the language. It provides debugging tools, auto-complete, code reloading, as well as nicely formatted documentation.

I used the book [Programming Elixir 1.3](https://pragprog.com/book/elixir13/programming-elixir-1-3) by Dave Thomas to learn about Elixir. In fact, Raven is actually an implementation copy of the project in [Chapter 13: Organizing a Project](https://www.safaribooksonline.com/library/view/programming-elixir-13/9781680502329/). I followed the example as described in the chapter to create Raven.

### Project

Raven is a simple CLI implementation to display GitHub issues for a particular project. It can be executed by passing the GitHub user and project as arguments. As an example, take a look at the screenshot of the app in action:

![Screenshot](https://raw.githubusercontent.com/donny/raven/master/screenshot.png)

### Implementation

Raven is implemented by three main files:

- [`cli.ex`](https://github.com/donny/raven/blob/master/lib/raven/cli.ex) that does CLI parsing and processing.
- [`table_formatter.ex`](https://github.com/donny/raven/blob/master/lib/raven/table_formatter.ex) that prints a table for the results.
- [`github_issues.ex`](https://github.com/donny/raven/blob/master/lib/raven/github_issues.ex) that fetches the data from GitHub and handles the response. The whole file can be seen below:

```elixir
defmodule Raven.GitHubIssues do

  require Logger

  @user_agent [ {"User-agent", "Raven"} ]
  @github_url Application.get_env(:raven, :github_url)

  def fetch(user, project) do
    Logger.info "Fetching user #{user}'s project #{project}"
    issues_url(user, project)
    |> HTTPoison.get(@user_agent)
    |> handle_response
  end

  def issues_url(user, project) do
    "#{@github_url}/repos/#{user}/#{project}/issues"
  end

  def handle_response({:ok, %{status_code: 200, body: body}}) do
    Logger.info "Successful response"
    Logger.debug fn -> inspect(body) end
    {:ok, Poison.Parser.parse!(body)}
  end

  def handle_response({_, %{status_code: status, body: body}}) do
    Logger.error "Error #{status} returned"
    {:error, Poison.Parser.parse!(body)}
  end
end
```

### Conclusion

...
