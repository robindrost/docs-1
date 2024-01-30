# [Livery Video iOS SDK](/ios-sdk#migration-guide)

## Migration from 1.4.1 to 2.0.0

#### Player Properties

##### Notable Changes

- Renamed the `akamaiToken` property to `akamaiLongToken`

#### Player Methods

##### Removals

- Removed `updateOptions(PlayerOptions)`

#### PlayerOptions

##### Notable Changes

- Renamed the `akamaiToken` property to `akamaiLongToken`
- Moved all the controls options properties to the new [`controls`](<(#controls-options)>) property, which contains:
  - cast
  - contact
  - error
  - fullscreen
  - mute
  - pip
  - quality

##### Removals

- Removed `maxRecoveryDelay`, `minRecoveryDelay`, `preload` and `seekThreshold` properties

#### PlayerDelegate

##### Removals

- Remove deprecated `activeQualityDidChange(activeQuality: Quality?)`

  - This will continue to be reported through the `activeQualityDidChange(activeQuality: Quality?, auto: Bool)`
    - Auto is `true` when it was changed through the ABR and `false` when it was changed manually

- Remove deprecated `bandwidthTest(result: Result<UInt32, Error>)`

- Remove deprecated `playbackRateDidChange(playbackRate: Float)`

- Remove deprecated `selectedQualityDidChange(selectedQuality: Quality?)`
  - Use the new `activeQualityDidChange(activeQuality: Quality?, auto: Bool)`
    - for this case the `auto` parameter will be `false` meaning the active quality was changed manualy

#### LiveryPlayerControlView

##### Notable Changes

- Added `pipButton`

##### Removals

- Removed `airplayButton` and `cameraButton` properties