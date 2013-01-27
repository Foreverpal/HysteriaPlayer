
Hysteria Player
=========

This class provides basic useful player functionality.

Features:

- You don't need write KVO again, just sending 3 or 4 blocks then you can handle player status.
- Ability to play previous PlayerItem.
- If player paused bacause buffering problems, auto-resume the playback of your PlayerItem when enough buffered. 
- Background playable enabled. (need to register your App supports background modes as "App plays audio")
- Returns you which index of items are playing.
- Long buffer/load time for PlayerItems in background.
- PlayModes: Repeat, RepeatOne, Shuffle.

It provides:

- PlayerItem cache management.
- Pre-buffer next PlayerItem. 

How to use
---------------

### import HysteriaPlayer.h

Just #import the HysteriaPlayer.h header, and call the initWithHandler: and setupWithGetterBlock: method before you starting play. Everything sets for you.

Hysterai Player
----------


```objective-c
#import "HysteriaPlayer.h"

...

- (void)viewDidLoad
{
    [super viewDidLoad];
    
	hysteriaPlayer = [[HysteriaPlayer alloc]
                      initWithHandlerPlayerReadyToPlay:^{
                          if (![hysteriaPlayer isPlaying]) {
                              [hysteriaPlayer play];
                              //[self syncPlayPauseButtons];
                          }
                      }
                      PlayerRateChanged:^{
                          //[self syncPlayPauseButtons];
                      }
                      CurrentItemChanged:^(AVPlayerItem * newItem) {
                          if (newItem != (id)[NSNull null]) {
                              //[self syncPlayPauseButtons];
                          }
                      }
                      ItemReadyToPlay:^{
                          if ([hysteriaPlayer pauseReason] == HysteriaPauseReasonUnknown) {
                              [hysteriaPlayer play];
                          }
                      }];
    

}

- (IBAction)playStaticArray:(id)sender
{
    [hysteriaPlayer removeAllItems];
    [hysteriaPlayer setupWithGetterBlock:^NSString *(NSUInteger index) {
        return [mp3Array objectAtIndex:index];
        
    } ItemsCount:[mp3Array count]];
    
    [hysteriaPlayer fetchAndPlayPlayerItem:0];
    [hysteriaPlayer setPLAYMODE_Repeat:YES];
}
```

### Using initWithHandlers:

With these four blocks, you can handle about the Player's status changed, including `status changed`, `rate changed`, `currentItem changed`.

__Status Changed__ :
It will be called when Player is ready to play the PlayerItem, so play it. If you have play/pause buttons, should update their status after you starting play.

__Rate Changed__ :
It will be called when player's rate changed, probely 1.0 to 0.0 or 0.0 to 1.0. Anyways you should update your interface to notice the user what's happening. Hysteria Player have `HysteriaPauseReason` to help you.
 
* HysteriaPauseReasonPlaying : Player is playing
* HysteriaPauseReasonForce : Player paused because you set Player's property `PAUSE_REASON_ForcePause = YES` to pause it.
* HysteriaPauseReasonUnknown : Player paused for unknown reason, usually it because Player is paused for buffering.

__CurrentItem Changed__ :
It will be called when player's currentItem changed. If you have artworks, playeritem's infos or play/pause buttons to display, you should update them when this be called.

__ItemReadyToPlay__ :
It will be called when `PlayerItem` is ready to play, this is optional.
 
### Using setupWithGetterBlock:

Before you starting play anything, set your datasource to Hysteria Player. This block will gives you a index that will be used (instantly play or pre-buffer). Returning a NSString format url is all you need to do.  
`ItemsCount` tells Hysteria Player the count of your datasource, you have to update it using `setItemsCount:(NSUInteger)count` if your datasource's count is modified.


Installation
------------

### Add Frameworks

Add CoreMedia.framework and AVFoundation.framework to your Link Binary With Libraries.

### Copy provided point1sec.mp3 file to your Supporting Files 

Ability to play the `first` PlayerItem when your application is resigned active but `first` PlayerItem is still buffering. 


## Licenses

All source code is licensed under the [MIT License](https://raw.github.com/rs/SDWebImage/master/LICENSE).