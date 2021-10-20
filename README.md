# es_extended v1 Final.
Version of es_extended v1 Final modified for Quasar Inventory.

# Modifications.
## es_extended/client/main.lua.
Completely remove this part.

```lua
Citizen.CreateThread(function()
	while true do
		Citizen.Wait(0)

		if IsControlJustReleased(0, 289) then
			if IsInputDisabled(0) and not isDead and not ESX.UI.Menu.IsOpen('default', 'es_extended', 'inventory') then
				ESX.ShowInventory()
			end
		end
	end
end)
```

## es_extended/server/classes/player.lua.
Replace `self.getMoney = function ()`.

```lua
self.getMoney = function()
	if self.getInventoryItem('cash') ~= "xPlayernil" then
		local cash = self.getInventoryItem('cash')
		if cash ~= nil then
			if self.getAccount('money').money ~= cash.count then
				self.setAccountMoney('money', cash.count)
			end
		else
			self.setAccountMoney('money', 0)
		end
	end
	return self.getAccount('money').money
end
```



## es_extended/server/classes/player.lua.
Replace `self.addMoney = function(money)`.

```lua
self.addMoney = function(money)
	money = ESX.Math.Round(money)
	self.addAccountMoney('money', money)
	if money >= 0 then
		self.addInventoryItem("cash", money)
		local cash = self.getInventoryItem('cash')
		if self.getAccount('money').money ~= cash.count then
			self.setAccountMoney('money', cash.count) 
		end
	end
end
```



## es_extended/server/classes/player.lua.
Replace `self.getInventory = function(minimal)`.

```lua
self.getInventory = function(minimal)
end
```



## es_extended/server/classes/player.lua.
Replace `self.addAccountMoney = function(accountName, money)`.

```lua
self.addAccountMoney = function(accountName, money)
	if money > 0 then
		if accountName == 'money' then
			self.addInventoryItem("cash", money)
			local cash = self.getInventoryItem('cash')
			if self.getAccount('money').money ~= cash.count then
				self.setAccountMoney('money', cash.count)
			end
		else
			if accountName == 'black_money' then
				self.addInventoryItem("black_money", money)
				local cash = self.getInventoryItem('black_money')
				if self.getAccount('black_money').money ~= cash.count then
					self.setAccountMoney('black_money', cash.count)
				end
			else
				local account = self.getAccount(accountName)
				if account then
					local newMoney = account.money + ESX.Math.Round(money)
					account.money = newMoney
		
					self.triggerEvent('esx:setAccountMoney', account)
				end
			end
		end
	end
end
```



## es_extended/server/classes/player.lua.
Replace `self.removeAccountMoney = function(accountName, money)`.

```lua
self.removeAccountMoney = function(accountName, money)
	if money > 0 then
		if accountName == 'money' then
			self.removeInventoryItem("cash", money)
			local cash = self.getInventoryItem('cash')
			if cash ~= nil then
				if self.getAccount('money').money ~= cash.count then
					self.setAccountMoney('money', cash.count)
				end
			else
				self.setAccountMoney('money', 0)
			end
		else
			if accountName == 'black_money' then
				self.removeInventoryItem("black_money", money)
				local cash = self.getInventoryItem('black_money')
				if cash ~= nil then
					if self.getAccount('black_money').money ~= cash.count then
						self.setAccountMoney('black_money', cash.count)
					end
				else
					self.setAccountMoney('black_money', 0)
				end
			else
				local account = self.getAccount(accountName)
				if account then
					local newMoney = account.money - ESX.Math.Round(money)
					account.money = newMoney
	
					self.triggerEvent('esx:setAccountMoney', account)
				end
			end
		end
	end
end
```


## es_extended/server/classes/player.lua.
Replace `self.getInventoryItem = function(name)`.

```lua
self.getInventoryItem = function(name)
        local Item = exports['qs-core']:GetItem(self.source, name)
        if Item == "xPlayernil" then
            return "xPlayernil"
        end
        if Item ~= nil then
            Item.count = Item.amount
            return Item
        end
        Item = {}
        Item.count = 0
        return Item
    end
```



## es_extended/server/classes/player.lua.
Replace `self.addInventoryItem = function(name, count)`.

```lua
self.addInventoryItem = function(name, count)
	TriggerEvent('inventory:server:addItem', self.source, name, count)
	if name == 'cash' then
		local cash = self.getInventoryItem('cash')
		if self.getAccount('money').money ~= cash.count then
			self.setAccountMoney('money', cash.count)
		end
	end
end
```



## es_extended/server/classes/player.lua.
Replace `self.removeInventoryItem = function(name, count)`.

```lua
self.removeInventoryItem = function(name, count)
	TriggerEvent('inventory:server:removeItem', self.source, name, count)
	if name == 'cash' then
		local cash = self.getInventoryItem('cash')
		if cash ~= nil then
			if self.getAccount('money').money ~= cash.count then
				self.setAccountMoney('money', cash.count)
			end
		else
			self.setAccountMoney('money', 0)
		end
	end
end
```



## es_extended/server/classes/player.lua.
Replace `self.setInventoryItem = function(name, count)`.

```lua
self.setInventoryItem = function(name, count)
end
```



## es_extended/server/classes/player.lua.
Replace `self.canCarryItem = function(name, count)`.

```lua
self.canCarryItem = function(name, count)
	return true
end
```



## es_extended/server/commands.lua.
We will completely remove the giveitem command, since Quasar Inventory has its own system.
Completely remove this command: `ESX.RegisterCommand('giveitem', 'admin', function(xPlayer, args, showError)`.

```lua
ESX.RegisterCommand('giveitem', 'admin', function(xPlayer, args, showError)
	args.playerId.addInventoryItem(args.item, args.count)
end, true, {help = _U('command_giveitem'), validate = true, arguments = {
	{name = 'playerId', help = _U('commandgeneric_playerid'), type = 'player'},
	{name = 'item', help = _U('command_giveitem_item'), type = 'item'},
	{name = 'count', help = _U('command_giveitem_count'), type = 'number'}
}})
```

## es_extended/server/functions.lua.
Replace `ESX.RegisterUsableItem`.

```lua
ESX.RegisterUsableItem = function(item, cb)
	TriggerEvent('inventory:server:useable', item, cb)
end
```
