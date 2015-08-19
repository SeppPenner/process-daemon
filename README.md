# Process::Daemon

`Process::Daemon` is a stable and helpful base class for long running tasks and daemons. Provides standard `start`, `stop`, `restart`, `status` operations.

[![Build Status](https://travis-ci.org/ioquatix/process-daemon.svg?branch=master)](https://travis-ci.org/ioquatix/process-daemon)
[![Code Climate](https://codeclimate.com/github/ioquatix/process-daemon.png)](https://codeclimate.com/github/ioquatix/process-daemon)
[![Coverage Status](https://coveralls.io/repos/ioquatix/process-daemon/badge.svg?branch=master)](https://coveralls.io/r/ioquatix/process-daemon?branch=master)

## Installation

Add this line to your application's Gemfile:

    gem 'process-daemon'

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install process-daemon

## Usage

Create a file for your daemon, e.g. `daemon.rb`:

	#!/usr/bin/env ruby
	
	require 'process/daemon'
	
	# Very simple XMLRPC daemon
	class XMLRPCDaemon < Process::Daemon
		def working_directory
			File.expand_path("../tmp", __FILE__)
		end
	
		def startup
			puts "Starting server..."
			
			@rpc_server = WEBrick::HTTPServer.new(
				:Port => 31337,
				:BindAddress => "0.0.0.0"
			)
			
			@listener = XMLRPC::WEBrickServlet.new
			
			@listener.add_handler("fourty-two") do |amount|
				"Hello World"
			end
			
			@rpc_server.mount("/RPC2", @listener)
			
			begin
				@rpc_server.start
			rescue Interrupt
				puts "Daemon interrupted..."
			ensure
				@rpc_server.shutdown
			end
		end
		
		def shutdown
			puts "Stopping the RPC server..."
			@rpc_server.stop
		end
	end
	
	XMLRPCDaemon.daemonize

Then run `daemon.rb start`. To stop the daemon, run `daemon.rb stop`.

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

## License

Released under the MIT license.

Copyright, 2015, by [Samuel G. D. Williams](http://www.codeotaku.com/samuel-williams).

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
