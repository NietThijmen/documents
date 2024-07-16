# ℹ️ ox\_logs

Thanks to trapson for the snippet.

Add the below snippet on:

```
ox_lib\imports\logger\server.lua
```

```lua
if service == 'fivemerr' then
    local key = GetConvar('fivemerr:key', '')

    if key ~= '' then
        local endpoint = 'https://api.fivemerr.com/v1/logs'

        local headers = {
            ['Authorization'] = key,
            ['Content-Type'] = 'application/json',
            ['User-Agent'] = 'ox_lib'
        }

        function lib.logger(source, event, message, ...)
            if not buffer then
                buffer = {}

                SetTimeout(500, function()
                    PerformHttpRequest(endpoint, function(status, _, _, response)
                        if status ~= 200 then 
                            if type(response) == 'string' then
                                response = json.decode(response) or response
                                badResponse(endpoint, status, response)
                            end
                        end
                    end, 'POST', json.encode(buffer), headers)

                    buffer = nil
                end)
            end

            buffer = {
                level = "info",
                message = event .. " - " .. message,
                resource = cache.resource,
                metadata = {
                    event = event,
                    tags = formatTags(source, ... and string.strjoin(',', string.tostringall(...)) or nil),
                }
            }
        end
    end
end
```

Add this to your server.cfg with your Logs API KEY.&#x20;

```
set ox:logger "fivemerr"
set fivemerr:key "API KEY HERE"
```

Preview:

<figure><img src="https://images-ext-1.discordapp.net/external/2tkDnEhasKWqVLqXZmdXj0scEb4ZEg84Pgj9DjJnDu4/https/trapson.pictures/images/png/GKmaN.png?format=webp&#x26;quality=lossless" alt=""><figcaption></figcaption></figure>