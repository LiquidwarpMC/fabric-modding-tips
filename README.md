# Fabric modding tips

## Compound tags
### Null values
>You should never store null in compound tags wince you end up becoming incompatable with sponge since their data processor system specifically expects the contents of a CompoundTag to be never null and will crash as a result.

*i509VCB, Fabric Discord*

>Mojang technically disallow null too they just aren't defensive with it. It's common knowledge that they use ParametersAreNonnullByDefault on all their packages. It's just stripped by proguard. Forge adds it back but Loom doesn't

*Chloe Dawn, Fabric Discord*

## Inventory
>Inventory syncing conventionally happens via the container. Any other type of syncing is only necessary when the client needs to know outside the GUI. Please don't use block entity updates and similar if you don't have to

*JamiesWhiteShirt, Fabric Discord*

## Mappings
>Chloe Dawn: My mod references bindTexture, but method_4618 became a pointer to bindTextureInner in 1.15.2?
>Thalia/Christie: Intermediaries are matched by method content. If mojang refactors a method into api/impl then the intermediary name will go to the impl method. The same thing happened with Biome#getTemperature in 1.14.4

*Thalia/Christie, Fabric Discord*
