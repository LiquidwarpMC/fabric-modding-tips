# Fabric modding tips

## Mixins
### Documentation
#### Annotations
[Inject](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/Inject.html)
[Redirect](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/Redirect.html)
[ModifyVariable](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/ModifyVariable.html)
[ModifyConstant](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/ModifyConstant.html)
[ModifyArg](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/ModifyArg.html)
[ModifyArgs](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/ModifyArgs.html)
[At](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/At.html)
[Shadow](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/Shadow.html)
[Constant](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/Constant.html)
[Accessor](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/gen/Accessor.html)
[Final](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/Final.html)
[Invoker](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/gen/Invoker.html)
[Mixin](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/Mixin.html)
[Mutable](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/Mutable.html)
[Overwrite](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/Overwrite.html)
[Slice](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/Slice.html)
[Unique](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/Unique.html)

#### Other
[CallbackInfo](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/callback/CallbackInfo.html)
[CallbackInfoReturnable](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/callback/CallbackInfoReturnable.html)
[InjectionPoint](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/InjectionPoint.html)
[Constant.Condition](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/Constant.Condition.html)
[LocalCapture](http://jenkins.liteloader.com/view/Other/job/Mixin/javadoc/org/spongepowered/asm/mixin/injection/callback/LocalCapture.html)

## Game architecture
### General
![Game architecture by 2xsaiko](/images/game_architecture-2xsaiko-fabric_discord.png?raw=true "Game architecture")
*2xsaiko, Fabric Discord*

## Compound tags
### Null values
>You should never store null in compound tags wince you end up becoming incompatable with sponge since their data processor system specifically expects the contents of a CompoundTag to be never null and will crash as a result.

*i509VCB, Fabric Discord*

>Mojang technically disallow null too they just aren't defensive with it. It's common knowledge that they use ParametersAreNonnullByDefault on all their packages. It's just stripped by proguard. Forge adds it back but Loom doesn't

*Chloe Dawn, Fabric Discord*

## Inventory
### Syncing
>Inventory syncing conventionally happens via the container. Any other type of syncing is only necessary when the client needs to know outside the GUI. Please don't use block entity updates and similar if you don't have to

*JamiesWhiteShirt, Fabric Discord*

### Show slot numbers during debug
Adding numbers to container slots in debug:
```
package [...].mixin.client;

import net.fabricmc.api.EnvType;
import net.fabricmc.api.Environment;
import net.fabricmc.loader.api.FabricLoader;
import net.minecraft.client.gui.screen.Screen;
import net.minecraft.client.gui.screen.ingame.ContainerProvider;
import net.minecraft.client.gui.screen.ingame.ContainerScreen;
import net.minecraft.container.Container;
import net.minecraft.container.Slot;
import net.minecraft.text.Text;
import org.spongepowered.asm.mixin.Final;
import org.spongepowered.asm.mixin.Mixin;
import org.spongepowered.asm.mixin.Shadow;
import org.spongepowered.asm.mixin.injection.At;
import org.spongepowered.asm.mixin.injection.Inject;
import org.spongepowered.asm.mixin.injection.callback.CallbackInfo;

@Mixin(ContainerScreen.class)
@Environment(EnvType.CLIENT)
public abstract class ContainerScreenMixin<T extends Container> extends Screen implements ContainerProvider<T>{
    private static final boolean DEBUG = FabricLoader.getInstance().isDevelopmentEnvironment();

    @Shadow @Final protected T container;
    @Shadow protected int x;
    @Shadow protected int y;

    private ContainerScreenMixin(Text title){
        super(title);
    }

    @Inject(
        method = "render",
        at = @At("TAIL")
    )
    private void render(int mouseX, int mouseY, float delta, CallbackInfo callbackInfo){
        if(DEBUG){
            for(Slot slot : container.slots){
                if(slot != null){
                    font.draw("" + slot.id, slot.xPosition + x, slot.yPosition + y, 0xFFFFFF);
                }
            }
        }
    }
}
```

*Gudenau, Fabric Discord*

## Mappings
>Chloe Dawn: My mod references bindTexture, but method_4618 became a pointer to bindTextureInner in 1.15.2?
>Thalia/Christie: Intermediaries are matched by method content. If mojang refactors a method into api/impl then the intermediary name will go to the impl method. The same thing happened with Biome#getTemperature in 1.14.4

*Thalia/Christie, Fabric Discord*
