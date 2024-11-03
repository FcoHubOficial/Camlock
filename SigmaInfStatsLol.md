local autoclickerEnabled = false  -- Variable to track if the autoclicker is enabled
local delayTime = 0.1  -- Adjust the delay (in seconds) between each "click"
local autoOpenEnabled = false  -- Variable to track if auto-open is enabled
local autoPrestigeEnabled = false  -- Variable to track if auto prestige is enabled

local panelVisible = true  -- Variable to track panel visibility

-- Function to open the egg
local function openEgg()
    local args = {
        [1] = workspace:WaitForChild("EggStands"):WaitForChild("Basic"),
        [2] = 3
    }
    game:GetService("ReplicatedStorage"):WaitForChild("OpenEgg"):FireServer(unpack(args))
end

-- Function to run the auto-open in a coroutine
local function startAutoOpen()
    while autoOpenEnabled do
        openEgg()  -- Calls the function to open the egg
        wait(0.5)  -- Waits for half a second between each egg opening (adjust as necessary)
    end
end

-- Function to run the autoclicker as a coroutine
local function startAutoclicker()
    while autoclickerEnabled do
        local args = {
            [1] = {
                ["Cash"] = 999999999999999999999999999999,
                ["Funny"] = "durpiei1"
            }
        }
        game:GetService("ReplicatedStorage"):WaitForChild("Add"):FireServer(unpack(args))
        wait(delayTime)  -- Waits for the specified delay before clicking again
    end
end

-- Function to run the auto prestige in a coroutine
local function startAutoPrestige()
    while autoPrestigeEnabled do
        local args = {
            [1] = 1
        }
        game:GetService("ReplicatedStorage"):WaitForChild("Prestige"):FireServer(unpack(args))
        wait(delayTime)  -- Waits for the specified delay before prestiging again
    end
end

-- Create ScreenGui to hold the buttons and notification
local screenGui = Instance.new("ScreenGui")
screenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Create a frame to act as a panel
local panelFrame = Instance.new("Frame")
panelFrame.Size = UDim2.new(0, 250, 0, 250)  -- Size for the panel
panelFrame.Position = UDim2.new(0.5, -125, 0.5, -125)  -- Center the panel
panelFrame.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
panelFrame.BackgroundTransparency = 0.1  -- Less transparency for better visibility
panelFrame.Parent = screenGui

-- Create the title bar
local titleBar = Instance.new("TextLabel")
titleBar.Size = UDim2.new(1, 0, 0, 40)  -- Full width, fixed height for title
titleBar.Position = UDim2.new(0, 0, 0, 0)  -- At the top of the panel
titleBar.BackgroundColor3 = Color3.fromRGB(70, 70, 70)  -- Darker background for contrast
titleBar.TextColor3 = Color3.fromRGB(255, 255, 255)  -- White text color
titleBar.Text = "Fco Hub's"  -- Title text
titleBar.TextSize = 24  -- Title text size
titleBar.Parent = panelFrame

-- Create a close button (X) on the title bar
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 40, 0, 40)  -- Size of the close button
closeButton.Position = UDim2.new(1, -40, 0, 0)  -- Positioned at the right of the title bar
closeButton.Text = "X"
closeButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)  -- Red color for close button
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)  -- White text color
closeButton.TextSize = 20  -- Text size
closeButton.Parent = titleBar

-- Make the panel draggable
local dragInput
local dragging = false
local dragStart = nil
local startPos = nil

local function updateDrag(input)
    local delta = input.Position - dragStart
    panelFrame.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
end

panelFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = panelFrame.Position

        -- Connect to the input changed event
        dragInput = game:GetService("UserInputService").InputChanged:Connect(updateDrag)
    end
end)

panelFrame.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
        dragInput:Disconnect()  -- Stop updating the position
    end
end)

-- Create buttons
local buttonSize = UDim2.new(0, 150, 0, 30)  -- Size for buttons
local buttonSpacing = 10  -- Space between buttons
local startButtonY = -50  -- Starting Y position for buttons

-- Create the "Start Autoclicker" button
local startButton = Instance.new("TextButton")
startButton.Size = buttonSize  -- Size of the button
startButton.Position = UDim2.new(0.5, -75, 0.5, startButtonY)  -- Centered horizontally, slightly adjusted down
startButton.Text = "Start Autoclicker"
startButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)  -- Green color for Start button
startButton.Parent = panelFrame

-- Create the "Stop Autoclicker" button
local stopButton = Instance.new("TextButton")
stopButton.Size = buttonSize  -- Size of the button
stopButton.Position = UDim2.new(0.5, -75, 0.5, startButtonY + buttonSize.Y.Offset + buttonSpacing)  -- Positioned below Start button
stopButton.Text = "Stop Autoclicker"
stopButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)  -- Red color for Stop button
stopButton.Parent = panelFrame

-- Create the "Auto Open Egg" button
local autoOpenButton = Instance.new("TextButton")
autoOpenButton.Size = buttonSize  -- Size of the button
autoOpenButton.Position = UDim2.new(0.5, -75, 0.5, startButtonY + 2 * (buttonSize.Y.Offset + buttonSpacing))  -- Positioned below Stop button
autoOpenButton.Text = "Auto Open Egg"
autoOpenButton.BackgroundColor3 = Color3.fromRGB(0, 0, 255)  -- Blue color for Auto Open button
autoOpenButton.Parent = panelFrame

-- Create the "Auto Prestige" button
local autoPrestigeButton = Instance.new("TextButton")
autoPrestigeButton.Size = buttonSize  -- Size of the button
autoPrestigeButton.Position = UDim2.new(0.5, -75, 0.5, startButtonY + 3 * (buttonSize.Y.Offset + buttonSpacing))  -- Positioned below Auto Open button
autoPrestigeButton.Text = "Auto Prestige"
autoPrestigeButton.BackgroundColor3 = Color3.fromRGB(255, 255, 0)  -- Yellow color for Auto Prestige button
autoPrestigeButton.Parent = panelFrame

-- Set up the Start button to enable the autoclicker
startButton.MouseButton1Click:Connect(function()
    if not autoclickerEnabled then
        autoclickerEnabled = true
        coroutine.wrap(startAutoclicker)()  -- Starts the autoclicker in a coroutine
    end
end)

-- Set up the Stop button to disable the autoclicker
stopButton.MouseButton1Click:Connect(function()
    autoclickerEnabled = false
end)

-- Set up the Auto Open button to toggle auto-open on and off
autoOpenButton.MouseButton1Click:Connect(function()
    autoOpenEnabled = not autoOpenEnabled  -- Toggle the state
    if autoOpenEnabled then
        autoOpenButton.Text = "Stop Auto Open Egg"  -- Change button text
        coroutine.wrap(startAutoOpen)()  -- Start the auto-open process in a coroutine
    else
        autoOpenButton.Text = "Auto Open Egg"  -- Reset button text
    end
end)

-- Set up the Auto Prestige button to toggle auto-prestige on and off
autoPrestigeButton.MouseButton1Click:Connect(function()
    autoPrestigeEnabled = not autoPrestigeEnabled  -- Toggle the state
    if autoPrestigeEnabled then
        autoPrestigeButton.Text = "Stop Auto Prestige"  -- Change button text
        coroutine.wrap(startAutoPrestige)()  -- Start the auto-prestige process in a coroutine
    else
        autoPrestigeButton.Text = "Auto Prestige"  -- Reset button text
    end
end)

-- Create the notification for the script
local notification = Instance.new("TextLabel")
notification.Size = UDim2.new(0, 450, 0, 60)  -- Size for notification
notification.Position = UDim2.new(0.5, -175, 0, 10)  -- Centered at the top middle of the panel
notification.BackgroundColor3 = Color3.fromRGB(0, 0, 0)  -- Black background for notification
notification.TextColor3 = Color3.fromRGB(255, 255, 255)  -- White text color
notification.TextSize = 20  -- Text size
notification.Text = "Subscribe to Fco Hub's in YouTube"  -- Notification text
notification.Visible = false  -- Initially hidden
notification.Parent = screenGui

-- Show the notification when the script runs
notification.Visible = true
wait(5)  -- Show for 5 seconds
notification.Visible = false  -- Hide after 5 seconds

-- Keybind to toggle the panel with "LeftCtrl"
game:GetService("UserInputService").InputBegan:Connect(function(input, gameProcessedEvent)
    if input.KeyCode == Enum.KeyCode.LeftControl and not gameProcessedEvent then
        panelVisible = not panelVisible
        panelFrame.Visible = panelVisible  -- Toggle panel visibility
    end
end)

-- Set up the close button to hide the panel
closeButton.MouseButton1Click:Connect(function()
    panelVisible = false
    panelFrame.Visible = false  -- Hide the panel
end)

game.StarterGui:SetCore("SendNotification", {
    Title = "Made by Fco Hub's";
    Text = "Lick my balls";

})
