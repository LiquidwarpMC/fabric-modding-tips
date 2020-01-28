# Fabric modding tips

## Compound tags
### Null values
>You should never store null in compound tags wince you end up becoming incompatable with sponge since their data processor system specifically expects the contents of a CompoundTag to be never null and will crash as a result.

*i509VCB, Fabric Discord*

>Mojang technically disallow null too they just aren't defensive with it. It's common knowledge that they use ParametersAreNonnullByDefault on all their packages. It's just stripped by proguard. Forge adds it back but Loom doesn't

*Chloe Dawn, Fabric Discord*
