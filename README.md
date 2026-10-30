# Fram
-- ตั้งค่าตัวแปรพื้นฐาน
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

-- สถานะการทำงานของสคริปต์
local _G.AutoFarm = true 

-- ฟังก์ชันสำหรับฟาร์ม
local function startAutoFarm()
    print("เริ่มระบบฟาร์มเงิน...")
    
    while _G.AutoFarm do
        task.wait(0.5) -- หน่วงเวลาเพื่อไม่ให้เกมแลคหรือโดนแบนจากการส่งคำสั่งเร็วเกินไป
        
        -- ตรวจสอบว่าตัวละครยังมีชีวิตอยู่หรือไม่
        if player.Character and player.Character:FindFirstChild("Humanoid") and player.Character.Humanoid.Health > 0 then
            
            -- [ตัวอย่าง] ค้นหามอนสเตอร์หรือเป้าหมายที่ใกล้ที่สุดใน Workspace
            local target = nil
            local shortestDistance = math.huge
            
            for _, enemy in pairs(workspace.Enemies:GetChildren()) do
                if enemy:FindFirstChild("HumanoidRootPart") and enemy:FindFirstChild("Humanoid") and enemy.Humanoid.Health > 0 then
                    local distance = (humanoidRootPart.Position - enemy.HumanoidRootPart.Position).Magnitude
                    if distance < shortestDistance then
                        shortestDistance = distance
                        target = enemy
                    end
                end
            end
            
            -- ถ้าเจอเป้าหมาย ให้เคลื่อนที่ไปหาและโจมตี
            if target then
                -- ย้ายตัวละครไปใกล้เป้าหมาย
                humanoidRootPart.CFrame = target.HumanoidRootPart.CFrame * CFrame.new(0, 5, 3)
                
                -- จำลองการโจมตี (ขึ้นอยู่กับระบบของแต่ละเกม เช่น เรียกใช้ RemoteEvent)
                -- ตัวอย่าง: game:GetService("ReplicatedStorage").RemoteEvent:FireServer("Attack", target)
            end
            
        else
            -- หากตัวละครตาย รอจนกว่าจะเกิดใหม่
            player.CharacterAdded:Wait()
            character = player.Character
            humanoidRootPart = character:WaitForChild("HumanoidRootPart")
            task.wait(2)
        end
    end
end

-- รันฟังก์ชันฟาร์มในรูปแบบ Thread แยก
task.spawn(startAutoFarm)
