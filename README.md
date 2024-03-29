> :warning: **Warning**
> <br>
> This repository was archived as it was getting dependabot alerts for libsecp256k1.
> <br>
> If you would like to use, please upgrade cargo packages as required first.

# Password manager

## Testing locally examples
```
# Run chain locally
docker run -it --rm -p 26657:26657 -p 26656:26656 -p 1337:1337 -v $(pwd):/root/code --name secretdev enigmampc/secret-network-sw-dev

# Access container via separate terminal window 
docker exec -it secretdev /bin/bash

# cd into code folder
cd code

# Store contracts required for test
secretcli tx compute store buttcoin.wasm.gz --from a --gas 3000000 -y --keyring-backend test
secretcli tx compute store butt-lode.wasm.gz --from a --gas 3000000 -y --keyring-backend test
secretcli tx compute store password-manager.wasm.gz --from a --gas 3000000 -y --keyring-backend test

# Get the contract's id
secretcli query compute list-code

# Init Buttcoin 
CODE_ID=1
INIT='{"name": "Buttcoin", "symbol": "BUTT", "decimals": 6, "initial_balances": [{"address": "secret18ztjhwmypkdrtc87nkmxp9sylkvgw606hnl03m", "amount": "1000000000000000000"},{"address": "secret1e2quu7ct7xk0yfr9pw0vf3rgyacmn73pd9tmwt", "amount": "1000000000000000000"}], "prng_seed": "testing"}'
secretcli tx compute instantiate $CODE_ID "$INIT" --from a --label "Buttcoin" -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt

# Set viewing key for Buttcoin
secretcli tx compute execute secret18vd8fpwxzck93qlwghaj6arh4p7c5n8978vsyg '{"set_viewing_key": { "key": "testing" }}' --from a -y --keyring-backend test
secretcli tx compute execute secret18vd8fpwxzck93qlwghaj6arh4p7c5n8978vsyg '{"set_viewing_key": { "key": "testing" }}' --from b -y --keyring-backend test

# Init BUTT lode
CODE_ID=2
INIT='{"viewing_key": "DoTheRightThing.", "time_delay": 5}'
secretcli tx compute instantiate $CODE_ID "$INIT" --from a --label "butt-lode" -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt

# Init password manager
CODE_ID=5
INIT='{"buttcoin": {"address": "secret18vd8fpwxzck93qlwghaj6arh4p7c5n8978vsyg", "contract_hash": "4CD7F64B9ADE65200E595216265932A0C7689C4804BE7B4A5F8CEBED250BF7EA"}, "butt_lode": {"address": "secret10pyejy66429refv3g35g2t7am0was7ya6hvrzf", "contract_hash": "99F94EDC0D744B35A8FBCBDC8FB71C140CFA8F3F91FAD8C35B7CC37862A4AC95"}}'
secretcli tx compute instantiate $CODE_ID "$INIT" --from a --label "password manager - btn.group" -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt

CONTRACT_INSTANCE_ADDRESS=secret1ulgw0td86nvs4wtpsc80thv6xelk76ut6us57w

# Set viewing key for password manager
secretcli tx compute execute $CONTRACT_INSTANCE_ADDRESS '{"set_viewing_key": {"key": "DoTheRightThing.", "padding": "ThereWillBeButt."}}' --from a -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt
secretcli tx compute execute $CONTRACT_INSTANCE_ADDRESS '{"set_viewing_key": {"key": "DoTheRightThing.", "padding": "ThereWillBeButt."}}' --from b -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt

# Query hints
secretcli query compute query $CONTRACT_INSTANCE_ADDRESS '{"hints": { "address": "secret18ztjhwmypkdrtc87nkmxp9sylkvgw606hnl03m", "key": "DoTheRightThing." }}'
secretcli query compute query $CONTRACT_INSTANCE_ADDRESS '{"hints": { "address": "secret1e2quu7ct7xk0yfr9pw0vf3rgyacmn73pd9tmwt", "key": "DoTheRightThing." }}'

# Create hint
secretcli tx compute execute secret18vd8fpwxzck93qlwghaj6arh4p7c5n8978vsyg '{"send": { "recipient": "secret1ulgw0td86nvs4wtpsc80thv6xelk76ut6us57w", "amount": "1000000", "msg": "eyJjcmVhdGUiOnsibGFiZWwiOiAiYXNkZiIsInVzZXJuYW1lIjogInp4Y3YiLCAicGFzc3dvcmQiOiAicXdlciIsICJub3RlcyI6ICJ0eXVpIn19" }}' --from a -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt
secretcli tx compute execute secret18vd8fpwxzck93qlwghaj6arh4p7c5n8978vsyg '{"send": { "recipient": "secret1ulgw0td86nvs4wtpsc80thv6xelk76ut6us57w", "amount": "1000000", "msg": "eyJjcmVhdGUiOnsibGFiZWwiOiAiYXNkZiIsInVzZXJuYW1lIjogInp4Y3YiLCAicGFzc3dvcmQiOiAicXdlciIsICJub3RlcyI6ICJ0eXVpIn19" }}' --from b -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt

# Show authentication
secretcli tx compute execute $CONTRACT_INSTANCE_ADDRESS '{"show": { "position": 1 }}' --from a -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt
secretcli tx compute execute $CONTRACT_INSTANCE_ADDRESS '{"show": { "position": 1 }}' --from b -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt

# Create hint
secretcli tx compute execute secret18vd8fpwxzck93qlwghaj6arh4p7c5n8978vsyg '{"send": { "recipient": "secret1ulgw0td86nvs4wtpsc80thv6xelk76ut6us57w", "amount": "1000000", "msg": "eyJjcmVhdGUiOnsibGFiZWwiOiAibWFzZGZhIiwpositionXNlcm5hbWUiOiAiQmFuZHkiLCAicGFzc3dvcmQiOiAiU3VtbWVyU2FmZSEhISEiLCAibm90ZXMiOiAiSGFyZCBmb3IgbWUgdG8gc2F5In19" }}' --from a -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt
secretcli tx compute execute secret18vd8fpwxzck93qlwghaj6arh4p7c5n8978vsyg '{"send": { "recipient": "secret1ulgw0td86nvs4wtpsc80thv6xelk76ut6us57w", "amount": "1000000", "msg": "eyJjcmVhdGUiOnsibGFiZWwiOiAibWFzZGZhIiwidXNlcm5hbWUiOiAiQmFuZHkiLCAicGFzc3dvcmQiOiAiU3VtbWVyU2FmZSEhISEiLCAibm90ZXMiOiAiSGFyZCBmb3IgbWUgdG8gc2F5In19" }}' --from b -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt

# Update authentication
secretcli tx compute execute $CONTRACT_INSTANCE_ADDRESS '{"update_authentication": { "position": 0, "id": "secret18ztjhwmypkdrtc87nkmxp9sylkvgw606hnl03m0", "label": "bastard", "username": "from", "password": "a", "notes": "basket" }}' --from a -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt
secretcli tx compute execute $CONTRACT_INSTANCE_ADDRESS '{"update_authentication": { "position": 1, "id": "secret1e2quu7ct7xk0yfr9pw0vf3rgyacmn73pd9tmwt1", "label": "bastard", "username": "from", "password": "a", "notes": "basket" }}' --from b -y --keyring-backend test --gas 3000000 --gas-prices=3.0uscrt
```

## References
1. Password manager: https://btn.group/secret_network/password_manager
2. Secret contracts guide: https://github.com/enigmampc/secret-contracts-guide
