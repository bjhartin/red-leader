#! /usr/local/bin/ruby

require 'rubygems'
require 'progressbar'
require 'colorize'

@time_in_minutes = ARGV[0].to_i
@reminder = ARGV[1]
@sleep_time = 60 
@progress = 0
@overtime = 0

def put(str)
  print str
  $stdout.flush
end

def beep()
  system('printf "\a"')
end

def display_progress_bar(time_in_minutes, sleep_time)
  pbar = ProgressBar.new("Remaining", time_in_minutes)
  time_in_minutes.times do
    sleep(sleep_time)
    beep() if (pbar.current + 1) % 5 == 0
    @progress = pbar.current
    pbar.inc
  end
  pbar
end

def notify(message)
  puts "Time's up!" 
  `terminal-notifier -message "#{message}: Time's up!" -title "Red Leader"`
end

def show_overtime(sleep_time)
  put "Minutes overtime:   |"

  while true do
    sleep(sleep_time)
    tick = (@overtime % 10 == 0) ? "O" : "o"
    tick = case @overtime
    when 0..10
      tick.colorize(:green)
    when 11..15
      tick.colorize(:yellow)
    else
      tick.colorize(:red)
    end
    print tick
    @overtime = @overtime + 1
  end
end

def capture_reason()
  puts "\nWhy did you take longer than your estimate?"
  STDIN.gets.chomp
end

def write_results(goal, estimate, actual)
  reason = if actual > estimate
    ", \"reason\": \"#{capture_reason()}\""
  end

  File.open(File.expand_path("~/.red-leader"), "a") do |f|
    f.puts "{\"goal\":\"#{goal}\", \"estimate\":\"#{estimate}\", \"actual\":\"#{actual}\"#{reason}}"
  end
end

begin
puts "Stay on target: #{@reminder}"
pbar = display_progress_bar(@time_in_minutes, @sleep_time)
pbar.finish
notify(@reminder)
show_overtime(@sleep_time)
rescue SystemExit, Interrupt => e
  write_results(@reminder, @time_in_minutes, @progress + @overtime + 1)
  puts "\nBye."
end
