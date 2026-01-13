classDiagram
    class GameManager {
        -bool isGameOver
        +EditText EditText
        +PauseManager pause
        +Start() void
        +GameOver() void
        +GameClear() void
        +Update() void
        -OnTriggerEnter2D(Collider2D) void
    }

    class PlayerController {
        +float moveSpeed
        +float jumpForce
        -Rigidbody2D rb
        -Collider2D playerCollider
        +GameObject text
        +LayerMask groundLayer
        +LayerMask EnemyLayer
        +Vector2 groundCheckOffset
        +float groundCheckRadius
        +float groundCheckRightExtra
        +SavePoint savePoint
        -Animator animator
        -bool isDisabled
        -bool isMoving
        -SpriteRenderer spriteRenderer
        -BoxCollider2D boxCollider
        +GameObject cameraObject
        +Start() void
        +Update() void
        -EnableMovementAfterDelay(float) IEnumerator
    }

    class Enemy {
        +float moveSpeed
        +float chaseDistance
        +float blockDetectionDistance
        +float blockStopBuffer
        +float horizontalContactYThreshold
        -Animator animator
        -SpriteRenderer spriteRenderer
        -Rigidbody2D rb
        -bool isStoppedByBlock
        -bool prevFlipX
        -bool prevFlipXInitialized
        -float prevEulerY
        +Start() void
        +Update() void
        -LookMoveDirection(Vector3) void
    }

    class PauseManager {
        +bool isPaused
        +GameObject PUI
        +UIDocument uiDoc
        -Button ReturnButton
        -Button ReturnTitleButton
        -Button CloseGameButton
        -Button RetryButton
        -Button SettingsButton
        +EditText EditText
        +AudioSource bgmSource
        -bool bgmWasPlaying
        +Start() void
        +Update() void
        -PauseGame() void
        -ResumeGame() void
    }

    class EditText {
        +bool isEdit
        -TextField editField
        -VisualElement lastRoot
        +PauseManager pause
        +GameObject Question
        +UIDocument displayUIDocument
        +string displayLabelName
        -Button ReturnEditor
        -List~RegexData~ regexList
        -List~DropDownData~ dropDownList
        -List~InputFields~ inputFields
        +Start() void
        +Update() void
        +OpenTextEditor() void
        -CheckAllAnswers() void
    }

    class MainCamera {
        +bool useScene4Lock
        +float scene4TriggerX
        +Vector3 scene4LockPosition
        +float scene4LockOrthoSize
        +float scene4LockLerp
        +Transform player
        +float smoothSpeed
        +Vector3 offset
        +bool minHeightCamera
        +float height
        +float low
        +float right
        +float left
        +float maxCameraMoveWidth
        +Start() void
        +Update() void
        -FollowPlayer() void
        -LimitCameraRange() void
    }

    class Goal {
        +static int goal
        -OnTriggerEnter2D(Collider2D) void
    }

    class Qswitch {
        +EditText targetEditor
        +GameObject respawnPrefab
        +Start() void
        +Update() void
        -OnCollisionEnter2D(Collision2D) void
    }

    class QswitchManager {
        +static QswitchManager Instance
        -GameObject lastPrefab
        -GameObject lastInstance
        -Vector3 lastPosition
        -Quaternion lastRotation
        -bool hasRecord
        +Awake() void
        +RecordDestroyed(GameObject, GameObject) void
        +RestoreLast() void
    }

    class RegexData {
        +string fieldName
        +string regexPattern
        +UnityEvent onCorrect
        +UnityEvent onIncorrect
    }

    class DropDownData {
        +string dropdownName
        +string correctChoice
        +UnityEvent onCorrect
        +UnityEvent onIncorrect
    }

    class InputFields {
        +string fieldName
        +string inputValue
    }

    GameManager --> EditText : references
    GameManager --> PauseManager : references
    GameManager --> Goal : triggers GameClear
    
    PlayerController --> Animator : uses
    PlayerController --> SpriteRenderer : uses
    PlayerController --> BoxCollider2D : uses
    PlayerController --> SavePoint : references
    PlayerController --> MainCamera : references
    
    Enemy --> Animator : uses
    Enemy --> SpriteRenderer : uses
    Enemy --> Rigidbody2D : uses
    Enemy --> PlayerController : chases
    
    PauseManager --> EditText : references
    PauseManager --> UIDocument : references
    
    EditText --> RegexData : contains
    EditText --> DropDownData : contains
    EditText --> InputFields : uses
    EditText --> PauseManager : references
    
    MainCamera --> PlayerController : follows
    
    Goal --> GameManager : calls GameClear
    Goal --> Scene : changes scene
    
    Qswitch --> EditText : opens
    Qswitch --> QswitchManager : records destroyed
    
    QswitchManager --> Qswitch : restores
```

## クラスの主要な関連性

### ゲームフロー
1. **GameManager**: ゲーム全体の状態管理（ゲームオーバー、ゲームクリア）
2. **PlayerController**: プレイヤーの操作・移動・ジャンプ
3. **Enemy**: 敵の動きと追跡ロジック
4. **Goal**: ゴール判定（シーン遷移）

### UI・ポーズシステム
1. **PauseManager**: ポーズ機能と UI 管理
2. **EditText**: テキスト入力・正規表現判定とドロップダウン判定
3. **Qswitch**: スイッチを踏むと EditText を開く
4. **QswitchManager**: スイッチの復活・破壊記録

### カメラシステム
1. **MainCamera**: プレイヤー追従とカメラ範囲制限

## 主要なインタラクション

| 関連 | 説明 |
|------|------|
| PlayerController → Qswitch | スイッチを踏むと検出 |
| Qswitch → EditText | EditText.OpenTextEditor() を呼出し |
| EditText → PauseManager | isPaused フラグで連動 |
| Enemy → PlayerController | プレイヤーを追跡 |
| Goal → GameManager | GameClear() を呼出してシーン遷移 |
| MainCamera → PlayerController | プレイヤー位置を追従 |
| QswitchManager → Qswitch | スイッチの復活を管理 |
