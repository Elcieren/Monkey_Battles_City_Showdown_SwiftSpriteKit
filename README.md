## Monkey_Battles_City_Showdown_SwiftSpriteKit
| Oyun Başlıyor | Oyun Bitti && Tekrardan Baslamak |
|---------|---------|
| ![Video 1](https://github.com/user-attachments/assets/b2e368e2-4fb3-40d2-97ae-6ce2f4567125) | ![Video 2](https://github.com/user-attachments/assets/704cbc9f-25cd-45a6-9e7d-72953e0cbd05) |

 <details>
    <summary><h2>Oyun Yapısı</h2></summary>
    Proje Amacı
   Amaç: Oyuncular sırayla muz fırlatarak karşı oyuncuyu vurmayı hedefler. Oyuncuların arasında çeşitli yüksekliklerde binalar bulunur ve bu binalar muzların yörüngesini engelleyebilir.
   Mekanikler:
   Fizik motoru: Oyunda fiziksel çarpışmalar ve yer çekimi etkileri SpriteKit'in fizik motoru ile yönetiliyor.
   Binalar: Rastgele boyut ve konumda binalar oluşturulur, oyuncular bu binaların tepelerinde yer alır.
   Sıra tabanlı oynanış: Oyuncular sırayla muz fırlatır, atış için açı ve hız belirlenir.
   Kazanan: Bir oyuncu karşı tarafı başarıyla vurursa, oyun o oyuncunun galibiyetiyle sonuçlanır.
  </details>  

  <details>
    <summary><h2>Değişkenler ve Tanımlar</h2></summary>
    GameScene Sınıfı: Oyunun ana sahnesini temsil eder. SKScene sınıfından türetilmiştir ve SpriteKit tarafından oyun dünyasını yönetmek için kullanılır.
    Değişkenler:
    buildings: Oyundaki bina nesnelerini saklar.
    player1 ve player2: İki oyuncunun karakterlerini temsil eder.
    banana: Fırlatılan nesneyi (muz) temsil eder.
    currentPlayer: Şu anki oyuncunun kim olduğunu belirler
    
    ```
    class GameScene: SKScene, SKPhysicsContactDelegate {
    var buildings = [BuildingNode]()
    weak var viewController: GameViewController?
    
    var player1: SKSpriteNode!
    var player2: SKSpriteNode!
    var banana: SKSpriteNode!
    
    var currentPlayer = 1
    }




    ```
  </details> 

  <details>
    <summary><h2>didMove(to:) Metodu</h2></summary>
    didMove(to:): Sahne yüklendiğinde çağrılır. Arka plan rengi ayarlanır, binalar oluşturulur ve oyuncular sahneye eklenir.
    physicsWorld.contactDelegate = self: Çarpışma algılama için delegasyon ayarlanır.
    
    ```
    override func didMove(to view: SKView) {
    backgroundColor = UIColor(hue: 0.669, saturation: 0.99, brightness: 0.67, alpha: 1)
    createBuildings()
    createPlayers()
    physicsWorld.contactDelegate = self
    }



    ```
  </details> 

  <details>
    <summary><h2>createBuildings()</h2></summary>
     createBuildings: Oyunda rastgele boyutlarda ve konumlarda bina nesneleri oluşturur.
     Bina Konumlandırma:
     currentX değişkeni kullanılarak binalar yatay eksende dizilir.
     Rastgele genişlik ve yükseklik değerleriyle bina boyutları belirlenir
    
    ```
         func createBuildings() {
    var currentX: CGFloat = -15
    while currentX < 1024 {
        let size = CGSize(width: Int.random(in: 2...4) * 40, height: Int.random(in: 300...600))
        currentX += size.width + 2
        let building = BuildingNode(color: .red, size: size)
        building.position = CGPoint(x: currentX - (size.width / 2), y: size.height / 2)
        building.setup()
        addChild(building)
        buildings.append(building)
    }
    }



    
    ```
  </details> 


  <details>
    <summary><h2>createPlayers()</h2></summary>
    createPlayers: Oyuncuları sahneye ekler.
    Her oyuncu, fiziksel özelliklerle donatılmış bir SKSpriteNode olarak oluşturulur.
    isDynamic = false: Oyuncuların çarpışmalardan etkilenmemesini sağlar.
    
    ```
       func createPlayers() {
    player1 = SKSpriteNode(imageNamed: "player")
    player1.name = "player1"
    player1.physicsBody = SKPhysicsBody(circleOfRadius: player1.size.width / 2)
    player1.physicsBody?.categoryBitMask = CollisionTypes.player.rawValue
    player1.physicsBody?.collisionBitMask = CollisionTypes.banana.rawValue
    player1.physicsBody?.contactTestBitMask = CollisionTypes.banana.rawValue
    player1.physicsBody?.isDynamic = false
    let player1Building = buildings[1]
    player1.position = CGPoint(x: player1Building.position.x, y: player1Building.position.y + ((player1Building.size.height + player1.size.height) / 2))
    addChild(player1)
    ...
    }



    ```
  </details> 

  <details>
    <summary><h2>launch(angle: Int, velocity: Int)</h2></summary>
    launch(angle:velocity:): Oyuncunun fırlatma açısına ve hızına göre muzun hareketini hesaplar.
    Yeni Muz Nesnesi:
    Eski muz varsa kaldırılır.
    Yeni bir banana nesnesi oluşturulur ve fiziksel etkileşimleri ayarlanır
    
    ```
        func launch(angle: Int, velocity: Int) {
    let speed = Double(velocity) / 10.0
    let radians = deg2rad(degrees: angle)
    if banana != nil {
        banana.removeFromParent()
        banana = nil
    }
    banana = SKSpriteNode(imageNamed: "banana")
    banana.name = "banana"
    banana.physicsBody = SKPhysicsBody(circleOfRadius: banana.size.width / 2)
    banana.physicsBody?.categoryBitMask = CollisionTypes.banana.rawValue
    banana.physicsBody?.collisionBitMask = CollisionTypes.building.rawValue | CollisionTypes.player.rawValue
    banana.physicsBody?.contactTestBitMask = CollisionTypes.building.rawValue | CollisionTypes.player.rawValue
    banana.physicsBody?.usesPreciseCollisionDetection = true
    addChild(banana)
    ...
    }



    ```
  </details> 

  <details>
    <summary><h2>GameScene.swift (Muzun Fırlatılması ve Hareketi)</h2></summary>
    Oyuncu Kontrolü: currentPlayer değişkeniyle hangi oyuncunun sırası olduğu kontrol edilir.
    Muzun Konumlandırılması:
    player1 için muz sola, player2 için sağa doğru yerleştirilir.
    Fırlatma İvmesi:
    applyImpulse ile muzun hareket vektörü belirlenir.
    Açılar cos ve sin fonksiyonlarıyla yatay ve dikey kuvvetlere bölünür.
    
    ```
        if currentPlayer == 1 {
    banana.position = CGPoint(x: player1.position.x - 30, y: player1.position.y + 40)
    banana.physicsBody?.angularVelocity = -20
    
    let impulse = CGVector(dx: cos(radians) * -speed, dy: sin(radians) * speed)
    banana.physicsBody?.applyImpulse(impulse)
    } else {
    banana.position = CGPoint(x: player2.position.x + 30, y: player2.position.y + 40)
    banana.physicsBody?.angularVelocity = 20
    
    let impulse = CGVector(dx: cos(radians) * speed, dy: sin(radians) * speed)
    banana.physicsBody?.applyImpulse(impulse)
    }




    ```
  </details> 

  <details>
    <summary><h2>GameScene.swift (Çarpışma Algılama)</h2></summary>
    didBegin Fonksiyonu:
    Çarpışma olduğunda çağrılır. Çarpışan nesneler bodyA ve bodyB olarak alınır.
     Sıralama:
     Bit maskeler karşılaştırılarak firstBody ve secondBody olarak atanır.
    Çarpışma Kontrolü:
    Muz bir binaya çarptığında bananaHitBuilding fonksiyonu çağrılır.
    
    ```
      func didBegin(_ contact: SKPhysicsContact) {
    let firstBody: SKPhysicsBody
    let secondBody: SKPhysicsBody
    
    if contact.bodyA.categoryBitMask < contact.bodyB.categoryBitMask {
        firstBody = contact.bodyA
        secondBody = contact.bodyB
    } else {
        firstBody = contact.bodyB
        secondBody = contact.bodyA
    }
    
    if let firstNode = firstBody.node, let secondNode = secondBody.node {
        if firstNode.name == "banana" && secondNode.name == "building" {
            bananaHitBuilding(secondNode as! BuildingNode, atPoint: contact.contactPoint)
        }
        ...
    }
    }





    ```
  </details> 

  <details>
    <summary><h2>GameScene.swift (Bina Hasarı İşleme)</h2></summary>
    bananaHitBuilding:
    Muzun bir binaya çarptığı durumlarda çağrılır.
    Hasar Hesaplama:
    Çarpışma noktası, binanın yerel koordinatlarına dönüştürülür (convert fonksiyonu).
    hit fonksiyonu çağrılarak bina hasarı işlenir.
    Muzun Kaldırılması:
    Çarpışmadan sonra muz sahneden kaldırılır.
    
    ```
         func bananaHitBuilding(_ building: BuildingNode, atPoint contactPoint: CGPoint) {
    let buildingLocation = convert(contactPoint, to: building)
    building.hit(at: buildingLocation)
    banana.name = nil
    banana.removeFromParent()
    banana = nil
    }




    ```
  </details> 

  


<details>
    <summary><h2>Uygulama Görselleri </h2></summary>
    
    
 <table style="width: 100%;">
    <tr>
        <td style="text-align: center; width: 16.67%;">
            <h4 style="font-size: 14px;">Oyun Basladi</h4>
            <img src="https://github.com/user-attachments/assets/aaf4cca5-9632-4819-8dc3-6e0f8df653d2" style="width: 100%; height: auto;">
        </td>
        <td style="text-align: center; width: 16.67%;">
            <h4 style="font-size: 14px;">Oyun Ortasi Evre</h4>
            <img src="https://github.com/user-attachments/assets/687e5c08-e451-4aab-bdc9-e2cbf7619775" style="width: 100%; height: auto;">
        </td>
    </tr>
</table>
  </details> 
