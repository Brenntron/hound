# Hound

For browser automation and writing integration tests in Elixir.

<a href="http://github.com/HashNuke/Hound" target="_parent">Source</a> | <a href="http://hashnuke.com/docs/hound/" target="_parent">Documentation</a>

[![Build Status](https://travis-ci.org/HashNuke/hound.png?branch=master)](https://travis-ci.org/HashNuke/hound)

## Features

* Can run __multiple browser sessions__ simultaneously. [See example](https://github.com/HashNuke/hound/blob/master/test/multiple_browser_session_test.exs).

* Supports Selenium (Firefox, Chrome), ChromeDriver and PhantomJs.

* Supports Javascript-heavy apps. Retries a few times before reporting error.

* Implements the WebDriver Wire Protocol.


**Internet Explorer may work under Selenium, but hasn't been tested.


#### Example

##### ExUnit example

```elixir
defmodule HoundTest do
  use ExUnit.Case
  use Hound.Helpers

  hound_session

  test "the truth", meta do
    navigate_to("http://example.com/guestbook.html")

    element_id = find_element(:name, "message")
    fill_field(element_id, "Happy Birthday ~!")
    submit_element(element_id)

    assert page_title() == "Thank you"
  end

end
```

Here's another [simple browser-automation example](https://github.com/HashNuke/hound/blob/master/notes/simple-browser-automation.md).

## Setup

Hound requires Elixir 1.0.2 or higher.

* Add dependency to your mix project

  ```elixir
  # If you are using hex
  { :hound, "~> 0.6.0" }

  # If you are not using hex
  { :hound, github: "HashNuke/hound", tag: "v0.6.0" }
  ```

* Add Hound to the list of applications to start in your `mix.exs`. Recommended to start Hound in test environment only.

  ```elixir
  def application do
    [ applications: app_list(Mix.env) ]
  end

  defp app_list(:test), do: [:hound | app_list]
  defp app_list(_),     do: app_list
  ```

When you run `mix tests`, Hound is automatically started. __You'll need a webdriver server__ running, like Selenium Server or Chrome Driver. If you aren't sure what it is, then [read this](https://github.com/HashNuke/hound/wiki/Starting-a-webdriver-server).

## Configure

To configure Hound, use your `config/config.exs` file or equivalent (v0.14.0 and above). [Examples are here](https://github.com/HashNuke/hound/blob/master/notes/configuring-hound.md).


## Usage

Add the following lines to your ExUnit test files.

```elixir
# Import helpers
use Hound.Helpers

# Start hound session and destroy when tests are run
hound_session
```

If you prefer to manually start and end sessions, use `Hound.start_session` and `Hound.end_session` in the setup and teardown blocks of your tests.


## Helpers

The documentation pages include examples under each function.

* [Navigation](http://hexdocs.pm/hound/Hound.Helpers.Navigation.html)
* [Page](http://hexdocs.pm/hound/Hound.Helpers.Page.html)
* [Element](http://hexdocs.pm/hound/Hound.Helpers.Element.html)
* [Cookies](http://hexdocs.pm/hound/Hound.Helpers.Cookie.html)
* [Javascript execution](http://hexdocs.pm/hound/Hound.Helpers.ScriptExecution.html)
* [Javascript dialogs](http://hexdocs.pm/hound/Hound.Helpers.Dialog.html)
* [Screenshot](http://hexdocs.pm/hound/Hound.Helpers.Screenshot.html)
* [Session](http://hexdocs.pm/hound/Hound.Helpers.Session.html)
* [Window](http://hexdocs.pm/hound/Hound.Helpers.Window.html)

The docs are at <http://hexdocs.pm/hound>.

### More examples? [Checkout Hound's own test cases](https://github.com/HashNuke/hound/tree/master/test/helpers)

## FAQ

#### Can I run multiple browser sessions simultaneously

Oh yeah ~! [Here is an example](https://github.com/HashNuke/hound/blob/master/test/multiple_browser_session_test.exs).

If you are running PhantomJs, take a look at the *Caveats* section below.

#### Can I run tests async?

Yes.

The number of tests you can run async at any point in time, depends on the number of sessions that your webdriver can maintain at a time. For Selenium Standalone, there seems to be a default limit of 15 sessions. You can set ExUnit's async option to limit the number of tests to run parallelly.

#### Will Hound guarantee an isolated session per test?

Yes. A separate session is started for each session.

## PhantomJs caveats

PhantomJs is extremely fast, but there are certain caveats. It uses Ghostdriver for it's webdriver server, which currently has unimplemented features or open issues.

* Cookie jar isn't seperate for sessions - <https://github.com/ariya/phantomjs/issues/11417>
  Which means all sessions share the same cookies. Make sure you run `delete_cookies()` at the end of each test.
* Isolated sessions were added to GhostDriver recently and are yet to land in a PhantomJs release.
* Javascript alerts aren't yet supported - <https://github.com/detro/ghostdriver/issues/20>.


## Customary proclamation...

Copyright &copy; 2013-2015, Akash Manohar J, under the MIT License (basically, do whatever you want)
