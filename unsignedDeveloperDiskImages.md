#how to load unsigned disk images to /Developer via Xcode

# Howto #

will post the deets here soon

Great. ive lost the offsets. fire up ida?


# Why #

To get access to /Developer/Library/Lockdown/ServiceAgents/com.apple**BEFORE they are READ and CACHED by xcode (they are cached :) )**

# Why this isnt enough #

becuase if debugserver dosent checkin with lockdownd (if you remove its' argument) then it 'fails to start' and everything shuts down

what does this mean?

--we cant change its arguments without a fatal crash
--we cant patch debugserver without a fatal crash.

what is going on?!?!? i dont know why apple CRC's and checks debugserver is running correctly but they sure are protecting it heavily...