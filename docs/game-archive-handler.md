# GameArchiveHandler integration contract

This fork of Mod Organizer 2's `modorganizer-uibase` adds the optional `MOBase::GameArchiveHandler` game feature. Its declaration is in [`include/uibase/game_features/gamearchivehandler.h`](../include/uibase/game_features/gamearchivehandler.h). The API is an interface for **game plugins** to expose their archive formats; it does not implement a format reader or writer in UIBase.

## Responsibilities

- `supportsArchive(const QString& archivePath)`: required. Decides whether the current game plugin recognizes one particular archive.
- `extractArchive(archivePath, outputDirectory, progress, errorMessage)`: required. Performs extraction, returning `false` on failure or cancellation.
- `supportedArchiveNameFilters()`: optional. Supplies file-dialog patterns for candidate archives. The default is empty; a consumer must still consult `supportsArchive()`.
- `canCreateArchive(archivePath)` and `createArchive(sourceDirectory, archivePath, progress, errorMessage)`: optional. Both return `false` by default for extract-only implementations.
- `ProgressCallback`: `std::function<void(qint64 current, qint64 total)>`; both units are determined by the implementing handler and must be interpreted consistently by its consumer.

The existing `DataArchives` and bsatk paths continue to handle Bethesda archives. A game plugin supplies format-specific validation, extraction, and optional creation; a consumer such as the corresponding `bsa_extractor` or `bsapacker` integration decides when to delegate.

## Integration and compatibility

`GameArchiveHandler` is derived from `details::GameFeatureCRTP<GameArchiveHandler>`, follows the existing MO2 game-feature registration model, and is forward-declared alongside other game features in `igamefeatures.h`.

The header intentionally declares the optional virtual methods after `supportsArchive()` and `extractArchive()` to preserve their existing vtable slot ordering. This is not a guarantee of binary interoperability between a plugin built against this fork and an unrelated upstream UIBase binary that lacks the interface. Build and load all consumers with a compatible set of UIBase headers and binaries.

Consumers should not infer support from archive extensions alone. They must use the active game's handler and treat a missing handler or `false` return as unavailable functionality. They must also preserve the standard `DataArchives` behavior when delegation is not appropriate.

## Related integration repositories

- [MO2 BSA Extractor archive dispatch](https://github.com/LegendMaster03/modorganizer-bsa_extractor-extractor-hook)
- [MO2 BSA Packer handler creation](https://github.com/LegendMaster03/modorganizer-bsapacker)
- [MO2 XnGine game integration](https://github.com/LegendMaster03/modorganizer-game_xngine)

These repositories implement or consume parts of the shared archive contract. They are separate projects, not source modules of UIBase.
