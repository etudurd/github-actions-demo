How to use it with JWT (GitHub OIDC)


'''
name: "Test Conjur via JWT" # replace with the one defined in conjur policy, example "release"
on: [push]

jobs:
  jwt-test:
    runs-on: ubuntu-latest
    permissions:
      id-token: write      # <== mandatory for OIDC
      contents: read

    steps:
      - uses: actions/checkout@v4

      - name: Fetch secrets from Conjur (JWT)
        uses: ./.github/actions+api/conjur-jwt-fetcher
        with:
          url: ${{ secrets.CONJUR_URL }}
          account: conjur
          certificate: ${{ secrets.CONJUR_CA_PEM }}   # optional
          authn_id: authn-jwt/github                  # must match your Conjur JWT authenticator id

          # optional: use a custom audience
          # custom_audience: "true"
          # audience: "my-custom-aud"

          secrets: >
            data/app/db-username|SQL_USERNAME;
            data/app/db-password|SQL_PASSWORD

      - name: Use the secrets
        run: |
          echo "User: $SQL_USERNAME"
          echo "Pass length: ${#SQL_PASSWORD}"



'''




ow to use it with API auth (host + API key)


'''

name: "Test Conjur via API auth"
on: [push]

jobs:
  api-test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Fetch secrets from Conjur (API auth)
        uses: ./.github/actions/conjur-jwt-fetcher
        with:
          url: ${{ secrets.CONJUR_URL }}
          account: conjur
          certificate: ${{ secrets.CONJUR_CA_PEM }}        # optional

          host_id: host/github-actions/runner-01           # same as in your Conjur policy
          api_key: ${{ secrets.CONJUR_API_KEY_RUNNER01 }}  # host's API key

          secrets: >
            data/app/db-username|SQL_USERNAME;
            data/app/db-password|SQL_PASSWORD

      - name: Use the secrets
        run: |
          echo "User: $SQL_USERNAME"
          echo "Pass length: ${#SQL_PASSWORD}"
'''

