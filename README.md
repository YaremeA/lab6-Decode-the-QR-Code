def decode_qr_code(qr_matrix):
    matrix_size = len(qr_matrix)  # Get the size of the QR matrix
    extracted_bits = []  # Store extracted bits

    # Traverse the QR matrix in zigzag order
    for row in range(matrix_size - 1, -1, -1):
        if (matrix_size - row) % 2 == 0:
            col_range = range(matrix_size - 1, -1, -1)
        else:
            col_range = range(matrix_size)

        for col in col_range:
            # Skip positioning patterns
            if (col < 7 and row < 7) or (col > matrix_size - 8 and row < 7) or (col < 7 and row > matrix_size - 8):
                continue

            # Apply mask condition
            mask_condition = (col + row) % 2 == 0
            bit = qr_matrix[row][col]
            if mask_condition:
                bit = 1 - bit  # Flip bit

            extracted_bits.append(bit)

    # Ensure extracted_bits is not empty
    if len(extracted_bits) < 12:
        raise ValueError("Insufficient data extracted from QR code")

    # Process mode and message length
    mode_bits = extracted_bits[:4]
    remaining_bits = extracted_bits[4:]
    if len(remaining_bits) < 8:
        raise ValueError("Insufficient bits for message length")
    message_length = int(''.join(map(str, remaining_bits[:8])), 2)

    # Extract message bits
    message_bits = remaining_bits[8:8 + message_length * 8]
    if len(message_bits) < message_length * 8:
        raise ValueError("Insufficient bits for the message")

    # Decode message bits to characters
    decoded_chars = []
    for i in range(0, len(message_bits), 8):
        char_bits = message_bits[i:i + 8]
        char_code = int(''.join(map(str, char_bits)), 2)
        decoded_chars.append(chr(char_code))

    return ''.join(decoded_chars)


# Example QR code matrix (ensure it matches the required size and structure)
qr_matrix_example = [
    [1, 1, 1, 1, 1, 1, 1, 0, 1, 1, 1, 0, 1, 0, 1, 1, 1, 1, 1, 1, 1],
    [1, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 1, 1, 0, 1, 0, 0, 0, 0, 0, 1],
    # Add the rest of the rows...
]

try:
    decoded_message = decode_qr_code(qr_matrix_example)
    print(decoded_message)
except ValueError as e:
    print(f"Error: {e}")
