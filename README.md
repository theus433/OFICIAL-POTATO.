# OFICIAL-POTATO. 





-- Normal punch 💬🔥🔥🔥
-- Local script put inside button 📝
-- Make sure name these 2 button is DownSlam and UpperCut ⚠️
-- Made by Potato dev😎👍
-- Like and Subscribe 🤯🥳🥳🥳



local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()  
local humanoid = character:WaitForChild("Humanoid")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local punchEvent = ReplicatedStorage:WaitForChild("PunchEvent")
 
local animations = {
"rbxassetid://218504594",   -- You can change the Animation if you want 📝
"rbxassetid://243827693",  
"rbxassetid://186934658",  
"rbxassetid://204062532"   
}
 
local secondPunchExtraAnimationId = "rbxassetid://128853357"  
local cooldowns = {0.5, 0.5, 0.5, 4}  -- This is CD you can changing it 📝
local damages = {5, 2.5, 2.5, 2.5}  -- This is Damage , you can changing it 📝
local clickCount = 0
local canPunch = true
 -- You can change sound if you want 📝
local missSoundId = "rbxassetid://5835032207"
local hitSoundIds = {
"rbxassetid://8595980577",  
"rbxassetid://8278630896",  
"rbxassetid://9117969687",  
"rbxassetid://9117969717"   
}
 
local screenGui = script.Parent.Parent
local downSlamButton = screenGui:FindFirstChild("DownSlam")
local upperCutButton = screenGui:FindFirstChild("UpperCut")
 
downSlamButton.Visible = false
upperCutButton.Visible = false
 
local function checkPunchHit()
    local rayOrigin = character.Head.Position  
    local rayDirection = character.PrimaryPart.CFrame.LookVector * 5 
    local ray = Ray.new(rayOrigin, rayDirection)
    local hitPart, hitPosition = workspace:FindPartOnRay(ray, character)
    
    if hitPart and hitPart.Parent then
        local hitHumanoid = hitPart.Parent:FindFirstChild("Humanoid")
        if hitHumanoid then
            return hitHumanoid  
        end
    end
    return nil  
end
 
local function playSound(soundId)
    local sound = Instance.new("Sound", character)
    sound.SoundId = soundId
    sound:Play()
    sound.Ended:Connect(function()
        sound:Destroy()  
    end)
end
 
local function resetPunch()
    clickCount = 0
end
 
local function punch()
    if canPunch then
        canPunch = false
        
        local punchAnimation = Instance.new("Animation")
        punchAnimation.AnimationId = animations[clickCount + 1]
        local animationTrack = humanoid:LoadAnimation(punchAnimation)
        
        if clickCount == 1 then
            local extraPunchAnimation = Instance.new("Animation")
            extraPunchAnimation.AnimationId = secondPunchExtraAnimationId
            local extraAnimationTrack = humanoid:LoadAnimation(extraPunchAnimation)
            
            if animationTrack then
                animationTrack:Play()  
            end
            if extraAnimationTrack then
                extraAnimationTrack:Play()  
            end
        else
            if animationTrack then
                animationTrack:Play()  
            end
        end
        
        if clickCount == 2 then
            animationTrack.Stopped:Connect(function()
                downSlamButton.Visible = true
                upperCutButton.Visible = true
            end)
        end
        
        local hitHumanoid = checkPunchHit()
        if hitHumanoid then
            playSound(hitSoundIds[clickCount + 1] or missSoundId)
        else
            playSound(missSoundId)
        end
        
        -- Notify the server to handle the punch logic and apply backward fling effect
        punchEvent:FireServer(damages[clickCount + 1], false, true, true) -- `true` enables backward fling effect
        
        clickCount = clickCount + 1  
        if clickCount >= #animations then
            clickCount = 0  
            wait(cooldowns[4])  
        else
            wait(cooldowns[clickCount])
        end
        
        canPunch = true  
    end
end
 
local function handleSpecialMove(selectedButton)
    downSlamButton.Visible = false
    upperCutButton.Visible = false
    resetPunch()
end
 
downSlamButton.MouseButton1Click:Connect(function()
    handleSpecialMove(downSlamButton)
end)
 
upperCutButton.MouseButton1Click:Connect(function()
    handleSpecialMove(upperCutButton)
end)
 
script.Parent.MouseButton1Click:Connect(function()
    if downSlamButton.Visible or upperCutButton.Visible then
        downSlamButton.Visible = false
        upperCutButton.Visible = false
    end
    punch()
end)
