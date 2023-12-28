# advance_avax-2

# Tokenvm Subnet using the Avalanche HyperSDK

# Adding things to consts.go file

`consts/consts.go`

```go
const (
	// TODO: choose a human-readable part for your hyperchain
	HRP = "Meta-Chain"
	// TODO: choose a name for your hyperchain
	Name = "Meta"
	// TODO: choose a token symbol
	Symbol = "MT"
)
```

### After making changes to consts.go file
```go
package consts

import (
	"github.com/ava-labs/avalanchego/ids"
	"github.com/ava-labs/avalanchego/vms/platformvm/warp"
	"github.com/ava-labs/hypersdk/chain"
	"github.com/ava-labs/hypersdk/codec"
	"github.com/ava-labs/hypersdk/consts"
)

const (
	// TODO: choose a human-readable part for your hyperchain
	HRP = "Meta-Chain"
	// TODO: choose a name for your hyperchain
	Name = "Meta"
	// TODO: choose a token symbol
	Symbol = "MT"
)

var ID ids.ID

func init() {
	b := make([]byte, consts.IDLen)
	copy(b, []byte(Name))
	vmID, err := ids.ToID(b)
	if err != nil {
		panic(err)
	}
	ID = vmID
}

// Instantiate registry here so it can be imported by any package. We set these
// values in [controller/registry].
var (
	ActionRegistry *codec.TypeParser[chain.Action, *warp.Message, bool]
	AuthRegistry   *codec.TypeParser[chain.Auth, *warp.Message, bool]
)

```
# Making changes to registry.go file 

`registry/registry.go`

```go
// TODO: register action: actions.CreateAsset
consts.ActionRegistry.Register(&actions.CreateAsset{}, actions.UnmarshalCreateAsset, false),

// TODO: register action: actions.MintAsset
consts.ActionRegistry.Register(&actions.MintAsset{}, actions.UnmarshalMintAsset, false),
```
### After making changes to registry.go file
```go
package registry

import (
	"github.com/ava-labs/avalanchego/utils/wrappers"
	"github.com/ava-labs/avalanchego/vms/platformvm/warp"
	"github.com/ava-labs/hypersdk/chain"
	"github.com/ava-labs/hypersdk/codec"

	"tokenvm/actions"
	"tokenvm/auth"
	"tokenvm/consts"
)

// Setup types
func init() {
	consts.ActionRegistry = codec.NewTypeParser[chain.Action, *warp.Message]()
	consts.AuthRegistry = codec.NewTypeParser[chain.Auth, *warp.Message]()

	errs := &wrappers.Errs{}
	errs.Add(
		// When registering new actions, ALWAYS make sure to append at the end.
		consts.ActionRegistry.Register(&actions.Transfer{}, actions.UnmarshalTransfer, false),


		// TODO: register action: actions.CreateAsset
		consts.ActionRegistry.Register(&actions.CreateAsset{}, actions.UnmarshalCreateAsset, false),

		// TODO: register action: actions.MintAsset
		consts.ActionRegistry.Register(&actions.MintAsset{}, actions.UnmarshalMintAsset, false),


		consts.ActionRegistry.Register(&actions.BurnAsset{}, actions.UnmarshalBurnAsset, false),
		consts.ActionRegistry.Register(&actions.ModifyAsset{}, actions.UnmarshalModifyAsset, false),

		consts.ActionRegistry.Register(&actions.CreateOrder{}, actions.UnmarshalCreateOrder, false),
		consts.ActionRegistry.Register(&actions.FillOrder{}, actions.UnmarshalFillOrder, false),
		consts.ActionRegistry.Register(&actions.CloseOrder{}, actions.UnmarshalCloseOrder, false),

		consts.ActionRegistry.Register(&actions.ImportAsset{}, actions.UnmarshalImportAsset, true),
		consts.ActionRegistry.Register(&actions.ExportAsset{}, actions.UnmarshalExportAsset, false),

		// When registering new auth, ALWAYS make sure to append at the end.
		consts.AuthRegistry.Register(&auth.ED25519{}, auth.UnmarshalED25519, false),
	)
	if errs.Errored() {
		panic(errs.Err)
	}
}
```

### Successfully Setup my subnet

- Step 1
Run ```MODE="run-single" ./scripts/run.sh```

- Step 2
Run ```./scripts/run.sh```

- Step 3
Load the demo private key included on the project ```./build/token-cli key import demo.pk``` and ```./build/token-cli chain import-anr```

## Interacting with the Subnet

### Creating Asset

```bash
./build/token-cli action create-asset
```

### Minting Tokens

```bash
./build/token-cli action mint-asset
```

### Checking Balance

```bash
./build/token-cli key balance
```

## Author : Yaswanth




