# kms decrypt in cli

```
KEY_ID=alias/my-key
SECRET_BLOB_PATH=fileb://my-secret-blob
SECRET_TEXT="my secret text"

ENCRYPTED_SECRET_AS_BLOB=encrypted_secret_blob
DECRYPTED_SECRET_AS_BLOB=decrypted_secret_blob  # Result of decrypt-blob target
```

encrypt-text:
	aws kms encrypt --key-id ${KEY_ID} --plaintext ${SECRET_TEXT} --query CiphertextBlob --output text \
		| base64 --decode > ${ENCRYPTED_SECRET_AS_BLOB}

decrypt-text:
	aws kms decrypt --ciphertext-blob fileb://${ENCRYPTED_SECRET_AS_BLOB} --query Plaintext --output text \
		| base64 --decode

encrypt-blob:
	aws kms encrypt --key-id ${KEY_ID} --plaintext ${SECRET_BLOB_PATH} --query CiphertextBlob --output text \
		| base64 --decode > ${ENCRYPTED_SECRET_AS_BLOB}

decrypt-blob:
	aws kms decrypt --ciphertext-blob fileb://${ENCRYPTED_SECRET_AS_BLOB} --query Plaintext --output text \
		| base64 --decode > ${DECRYPTED_SECRET_AS_BLOB}
