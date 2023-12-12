# Authorization function
def authorize_request():
    # Get the authorization token from the headers
    authorization_header = request.headers.get('Authorization')

    # Check if the authorization token is present
    if not authorization_header:
        return None

    # Assuming the authorization header is in the format "Bearer <refresh_token>"
    _, refresh_token = authorization_header.split(' ', 1)  # Splitting to get the refresh token

    # Query user based on refresh_token
    with mysql.connection.cursor() as cur:
        cur.execute("SELECT * FROM users WHERE refresh_token = %s", (refresh_token,))
        user = cur.fetchone()

    if not user:
        return None

    return user['UserID']

# ...

@app.route('/health', methods=['POST'])
def health():
    # ...

    # Authorize the request
    userID = authorize_request()

    if not userID:
        return jsonify({"status": "error", "message": "Invalid refresh token"}), 401

    # Rest of your code...

    return jsonify({
        "status": "success",
        "message": "Data inserted successfully",
        "current_point": current_point,
        "userID": userID
    })

# ...
