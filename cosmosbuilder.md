#Cosmos Builder

This tool allows new developers to get up and running with a CosmosSDK Application fast. 

A simple CLI builder walks you through the process of setting up the application with the following customisations:

- Application Path
- Application Name
- Service Name and Abbreviation
- Token Name

The application is a simple property registry where ownership is unique (think DNS, inventory, house or vehicle registry). This serves as an excellent starting point to building your own application.

The following transaction types will be created in the process:

- New: creates a new property entry to store in state
- Set: updates the name of the property entry
- Get: queries the stored state
- Buy: update the owner of the entry with a corresponding transaction

It also allows you to:

- AddData: adds a new data field to an existing data type with a corresponding Set and Get transaction
- AddType: add a new data type with corresponding New, Set, Get and Buy transactions

## Set up a new app

Where would you like to build your app? (Github user name): `user`

```
$appPath = input
mkdir github.com/$appPath && cd github.com/$appPath
git clone https://github.com/cosmos/sdk-application-tutorial.git
```

What would you like to call your App name? `app`

```
$appName = input
cp sdk-application-tutorial $appName && rm -r sdk-application-tutorial && cd $appName
cp x/nameservce x/{$appName}service
rename all nameservice -> {$appName}service
rename all Nameservice -> {$appName}service
rename all namesvc -> {$appName}svc
rename all nameServiceApp -> {$appName}ServiceApp
rename all NewNameServiceApp -> New{$appName}ServiceApp
```

What would you like to abbreviate your Service to? `abbv`

```
$abbv = input
rename all nsd -> {$abbv}d
cp x/cmd/nsd x/cmd/{$abbv}d
rename all nscli -> {$abbv}cli
cp x/cmd/nscli x/cmd/{$abbv}cli
rename all nsclient -> {$abbv}client
rename all nsrest -> {$abbv}rest
rename all storeNS -> store{$ABBV}
rename all keyNS -> key{$ABBV}
rename all NS -> {$ABBV}
rename all ns -> {$abbv}
rename all nsKeeper -> {$abbv}Keeper
```

If you have a token, what is it called? (enter to skip) `token`

```
$token = input
rename all nametoken -> {$token}
```

## Set a new Store Type

What is a unique instance of your data type called? (eg: DomainName) `typename`

```
$typename = input
$TypeName = SentenceCaps(input)
```

What is your new data store type called? (eg: DomainData) `type`

```
$type = input
$Type = SentenceCaps(input)
```



**x/types.go**
```
add:

// {$type} is a struct that contains all the metadata of a {$type}
type {$Type} struct {
	$TypeName string     `json:"$typename"`
	Owner sdk.AccAddress  `json:"owner"`
}

// Returns a new {$typeStruct} with {$TypeName} as "Hello World"
func New{$Type}() {$Type} {
	return {$Type}{
		{$TypeName}: 'Hello World',
	}
}
```

**x/keeper.go**
```
// Gets the entire {$Type} metadata struct for a {$Type}
func (k Keeper) Get{$Type}(ctx sdk.Context, musigname string) {$Type} {
	store := ctx.KVStore(k.storeKey)
	if !store.Has([]byte({$typename})) {
		return New{$Type}()
	}
	bz := store.Get([]byte({$typename}))
	var {$type} {$Type}
	k.cdc.MustUnmarshalBinaryBare(bz, {$type})
	return {$type}
}

// Sets the entire {$Type} metadata struct for a {$typename}
func (k Keeper) Set{$Type}(ctx sdk.Context, {$typename} string, {$type} {$Type}) {
	if {$type}.Owner.Empty() {
		return
	}
	store := ctx.KVStore(k.storeKey)
	store.Set([]byte({$typename}), k.cdc.MustMarshalBinaryBare({$type}))
}
```


**cli/rest.go**
```
add:
type set{$Data}Req struct {
	BaseReq rest.BaseReq `json:"base_req"`
	{$Data}    string       `json:"{$data}"`
	Owner   string       `json:"owner"`
}

func set{$Data}Handler(cdc *codec.Codec, cliCtx context.CLIContext) http.HandlerFunc {
	return func(w http.ResponseWriter, r *http.Request) {
		var req set{$Data}Req
		if !rest.ReadRESTReq(w, r, cdc, &req) {
			rest.WriteErrorResponse(w, http.StatusBadRequest, "failed to parse request")
			return
		}

		baseReq := req.BaseReq.Sanitize()
		if !baseReq.ValidateBasic(w) {
			return
		}

		addr, err := sdk.AccAddressFromBech32(req.Owner)
		if err != nil {
			rest.WriteErrorResponse(w, http.StatusBadRequest, err.Error())
			return
		}

		// create the message
		msg := {$appName}service.NewMsgSet{$Data}(req.{$Data}, req.Owner, addr)
		err = msg.ValidateBasic()
		if err != nil {
			rest.WriteErrorResponse(w, http.StatusBadRequest, err.Error())
			return
		}

		clientrest.WriteGenerateStdTxResponse(w, cdc, cliCtx, baseReq, []sdk.Msg{msg})
	}
}
```


**x/msgs.go**
```
// MsgSet{$Data} defines a Set{$Data} message
type MsgSet{$Data} struct {
	Data string
	Owner sdk.AccAddress
}

// NewMsgSet{$Data} is a constructor function for MsgSetSigner
func NewMsgSet{$Data}(data string, owner sdk.AccAddress) MsgSet{$Data} {
	return MsgSet{$Data}{
		Data:  data,
		Owner: owner,
	}
}

// Route should return the name of the module
func (msg MsgSet{$Data}) Route() string { return "{$appName}service" }

// Type should return the action
func (msg MsgSet{$Data}) Type() string { return "set_{$data}" }

// ValidateBasic runs stateless checks on the message
func (msg MsgSet{$Data}) ValidateBasic() sdk.Error {
	if msg.Owner.Empty() {
		return sdk.ErrInvalidAddress(msg.Owner.String())
	}
	if len(msg.{$Data}) == 0 {
		return sdk.ErrUnknownRequest("{$Data} cannot be empty")
	}
	return nil
}

// GetSignBytes encodes the message for signing
func (msg MsgSet{$Data}) GetSignBytes() []byte {
	b, err := json.Marshal(msg)
	if err != nil {
		panic(err)
	}
	return sdk.MustSortJSON(b)
}

// GetSigners defines whose signature is required
func (msg MsgSet{$Data}) GetSigners() []sdk.AccAddress {
	return []sdk.AccAddress{msg.Owner}
}
```

**x/handler.go.NewHandler()**
```
add:

case MsgSet{$Data}:
  return handleMsgSet{$Data}(ctx, keeper, msg)
```

**x/handler.go**
```
add:
// Handle a message to set {$Data}
func handleMsgSet{$Data}(ctx sdk.Context, keeper Keeper, msg MsgSet{$Data}) sdk.Result {
	if !msg.Owner.Equals(keeper.GetOwner(ctx, msg.{$Data})) { // Checks if the the msg sender is the same as the current owner
		return sdk.ErrUnauthorized("Incorrect Owner").Result() // If not, throw an error
	}
	keeper.Set{$Data}(ctx, msg.{$Data}) // If so, set the {$Data} specified in the msg.
	return sdk.Result{}                      // return
}
```



## Tx Builder

What is the type of transaction do you want to create? (new, set, get, buy)?

If `new`:

### Create a new Type Instance

**x/module_client.go.GetTxCmd(AddCommand(client.PostCommands()))**
```
add:
{$appName}servicecmd.GetCmdNew{$Data}(mc.cdc),
```

**cli/rest.go.RegisterRoutes()**
```
cdc.RegisterConcrete(MsgNew{$Data}, "{$appName}service/New{$Data}", nil)
```

**cli/tx.go**
```
add:

// GetCmdNew{$Data} is the CLI command for sending a New{$Data} transaction
func GetCmdNew{$Data}(cdc *codec.Codec) *cobra.Command {
	return &cobra.Command{
		Use:   "new-{$data} [{$typename}] [{$Data}]",
		Short: "Creae a new {$typename}",
		Args:  cobra.ExactArgs(2),
		RunE: func(cmd *cobra.Command, args []string) error {
			cliCtx := context.NewCLIContext().WithCodec(cdc).WithAccountDecoder(cdc)

			txBldr := authtxb.NewTxBuilderFromCLI().WithTxEncoder(utils.GetTxEncoder(cdc))

			if err := cliCtx.EnsureAccountExists(); err != nil {
				return err
			}

			msg := musigservice.NewMsgNew{$Data}(args[0], args[1], cliCtx.GetFromAddress())
			err = msg.ValidateBasic()
			if err != nil {
				return err
			}

			cliCtx.PrintResponse = true

			return utils.GenerateOrBroadcastMsgs(cliCtx, txBldr, []sdk.Msg{msg}, false)
		},
	}
}
```




If `set`:

### Set a new TX

What is the data you want to set called? (MsgText) `data`

```
$data = input
$Data = SentenceCaps(input)
```

**x/module_client.go.GetTxCmd(AddCommand(client.PostCommands()))**
```
add:
{$appName}servicecmd.GetCmdSet$Data(mc.cdc),
```

**cli/rest.go.RegisterRoutes()**
```
add:
r.HandleFunc(fmt.Sprintf("/%s/{$Data}", storeName), set{$Data}Handler(cdc, cliCtx)).Methods("PUT")
```

**cli/tx.go**
```
add:
// GetCmdSet{$Data} is the CLI command for sending a Set{$Data} transaction
func GetCmdSet{$Data}(cdc *codec.Codec) *cobra.Command {
	return &cobra.Command{
		Use:   "set-{$data} [data]",
		Short: "Set some data",
		Args:  cobra.ExactArgs(1),
		RunE: func(cmd *cobra.Command, args []string) error {
			cliCtx := context.NewCLIContext().WithCodec(cdc).WithAccountDecoder(cdc)

			txBldr := authtxb.NewTxBuilderFromCLI().WithTxEncoder(utils.GetTxEncoder(cdc))

			if err := cliCtx.EnsureAccountExists(); err != nil {
				return err
			}

			msg := {$appName}service.NewMsgSet{$Data}(args[0], cliCtx.GetFromAddress())
			err := msg.ValidateBasic()
			if err != nil {
				return err
			}

			cliCtx.PrintResponse = true

			// return utils.CompleteAndBroadcastTxCLI(txBldr, cliCtx, msgs)
			return utils.GenerateOrBroadcastMsgs(cliCtx, txBldr, []sdk.Msg{msg}, false)
		},
	}
}
```

**cli/rest.go**
```
add:
type set{$Data}Req struct {
	BaseReq rest.BaseReq `json:"base_req"`
	Data    string       `json:"data"`
	Owner   string       `json:"owner"`
}

func set{$Data}Handler(cdc *codec.Codec, cliCtx context.CLIContext) http.HandlerFunc {
	return func(w http.ResponseWriter, r *http.Request) {
		var req set{$Data}Req
		if !rest.ReadRESTReq(w, r, cdc, &req) {
			rest.WriteErrorResponse(w, http.StatusBadRequest, "failed to parse request")
			return
		}

		baseReq := req.BaseReq.Sanitize()
		if !baseReq.ValidateBasic(w) {
			return
		}

		addr, err := sdk.AccAddressFromBech32(req.Owner)
		if err != nil {
			rest.WriteErrorResponse(w, http.StatusBadRequest, err.Error())
			return
		}

		// create the message
		msg := {$appName}service.NewMsgSet{$Data}(req.{$Data}, req.Owner, addr)
		err = msg.ValidateBasic()
		if err != nil {
			rest.WriteErrorResponse(w, http.StatusBadRequest, err.Error())
			return
		}

		clientrest.WriteGenerateStdTxResponse(w, cdc, cliCtx, baseReq, []sdk.Msg{msg})
	}
}
```


**x/msgs.go**
```
// MsgSet{$Data} defines a Set{$Data} message
type MsgSet{$Data} struct {
	Data string
	Owner sdk.AccAddress
}

// NewMsgSet{$Data} is a constructor function for MsgSetSigner
func NewMsgSet{$Data}(data string, owner sdk.AccAddress) MsgSet{$Data} {
	return MsgSet{$Data}{
		Data:  data,
		Owner: owner,
	}
}

// Route should return the name of the module
func (msg MsgSet{$Data}) Route() string { return "{$appName}service" }

// Type should return the action
func (msg MsgSet{$Data}) Type() string { return "set_{$data}" }

// ValidateBasic runs stateless checks on the message
func (msg MsgSet{$Data}) ValidateBasic() sdk.Error {
	if msg.Owner.Empty() {
		return sdk.ErrInvalidAddress(msg.Owner.String())
	}
	if len(msg.{$Data}) == 0 {
		return sdk.ErrUnknownRequest("{$Data} cannot be empty")
	}
	return nil
}

// GetSignBytes encodes the message for signing
func (msg MsgSet{$Data}) GetSignBytes() []byte {
	b, err := json.Marshal(msg)
	if err != nil {
		panic(err)
	}
	return sdk.MustSortJSON(b)
}

// GetSigners defines whose signature is required
func (msg MsgSet{$Data}) GetSigners() []sdk.AccAddress {
	return []sdk.AccAddress{msg.Owner}
}
```

**x/handler.go.NewHandler()**
```
add:

case MsgSet{$Data}:
  return handleMsgSet{$Data}(ctx, keeper, msg)
```

**x/handler.go**
```
add:
// Handle a message to set {$Data}
func handleMsgSet{$Data}(ctx sdk.Context, keeper Keeper, msg MsgSet{$Data}) sdk.Result {
	if !msg.Owner.Equals(keeper.GetOwner(ctx, msg.{$Data})) { // Checks if the the msg sender is the same as the current owner
		return sdk.ErrUnauthorized("Incorrect Owner").Result() // If not, throw an error
	}
	keeper.Set{$Data}(ctx, msg.{$Data}) // If so, set the {$Data} specified in the msg.
	return sdk.Result{}                      // return
}
```
