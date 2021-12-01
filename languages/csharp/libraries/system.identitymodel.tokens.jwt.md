# System.IdentityModel.Tokens.Jwt

## Token Service

- creating a token service from scratch can be scary and confusing
- here is one I created that should help out with the basics of creating access and refresh jwts

```cs
    public class TokenConfig
    {
        public string SigningKey { get; set; }
        public int ExpiryTimeInMinutes { get; set; }
    }

    public class JwtConfig
    {
        public string Audience { get; set; }
        public string Issuer { get; set; }
        public TokenConfig AccessTokenConfig { get; set; }
        public TokenConfig RefreshTokenConfig { get; set; }
        public TokenValidationParameters AccessTokenValidationParameters
            => CreateTokenValidationParameters(AccessTokenConfig);
        public TokenValidationParameters RefreshTokenValidationParameters
            => CreateTokenValidationParameters(RefreshTokenConfig);

        public JwtSecurityToken CreateTokenDescriptor(TokenConfig tokenConfig, Claim[] claims = null)
            => new(
                Issuer,
                Audience,
                claims,
                expires: DateTime.UtcNow.AddMinutes(tokenConfig.ExpiryTimeInMinutes),
                signingCredentials: new SigningCredentials(
                    CreateSecurityKey(tokenConfig.SigningKey),
                    SecurityAlgorithms.HmacSha256Signature
                )
            );

        private TokenValidationParameters CreateTokenValidationParameters(TokenConfig tokenConfig)
            => new()
            {
                ValidateAudience = true,
                ValidAudience = Audience,
                ValidateIssuer = true,
                ValidIssuer = Issuer,
                ValidateIssuerSigningKey = true,
                IssuerSigningKey = CreateSecurityKey(tokenConfig.SigningKey),
                ValidateLifetime = true,
                ClockSkew = TimeSpan.Zero
            };

        private static SymmetricSecurityKey CreateSecurityKey(string signingKey)
            => new (Encoding.UTF8.GetBytes(signingKey));
    }
    
    public class TokenData
    {
        public string Token { get; set; }
        public DateTime Expires { get; set; }
    }

    public interface ITokenService
    {
        public (TokenData accessTokenData, TokenData refreshTokenData) GenerateTokens(User user);
        public bool IsValidRefreshToken(string refreshToken, out Guid userId);
    }

    public class TokenService : ITokenService
    {
        private readonly JwtConfig _jwtConfig;
        private readonly JwtSecurityTokenHandler _tokenHandler;

        public TokenService(JwtSecurityTokenHandler tokenHandler, IOptions<JwtConfig> jwtConfig)
        {
            _tokenHandler = tokenHandler;
            _jwtConfig = jwtConfig.Value;
        }

        public (TokenData accessTokenData, TokenData refreshTokenData) GenerateTokens(User user)
            => (
                GenerateAccessToken(user),
                GenerateRefreshToken(user)
            );

        public bool IsValidRefreshToken(string refreshToken, out Guid userId)
        {
            userId = default;
            try
            {
                _tokenHandler.ValidateToken(
                    refreshToken,
                    _jwtConfig.RefreshTokenValidationParameters,
                    out var validatedToken
                );
                return TryParseUserIdFromJwt((JwtSecurityToken)validatedToken, out userId);
            }
            catch (Exception)
            {
                return false;
            }
        }

        private TokenData GenerateAccessToken(User user)
            => GenerateToken(
                _jwtConfig.AccessTokenConfig,
                new[]
                {
                    new Claim(ClaimTypes.Name, user.UserName),
                    new Claim(ClaimTypes.NameIdentifier, user.Id.ToString())
                }
            );

        private TokenData GenerateRefreshToken(User user)
            => GenerateToken(
                _jwtConfig.RefreshTokenConfig,
                new[]
                {
                    new Claim(ClaimTypes.Name, user.UserName),
                    new Claim(ClaimTypes.NameIdentifier, user.Id.ToString())
                }
            );

        private TokenData GenerateToken(TokenConfig tokenConfig, Claim[] claims = null)
        {
            var tokenDescriptor = _jwtConfig.CreateTokenDescriptor(tokenConfig, claims);

            var token = _tokenHandler.WriteToken(tokenDescriptor);

            return new TokenData
            {
                Token = token,
                Expires = tokenDescriptor.ValidTo
            };
        }

        private bool TryParseUserIdFromJwt(JwtSecurityToken token, out Guid userId)
        {
            var userIdClaim = token.Claims.SingleOrDefault(x => x.Type == ClaimTypes.NameIdentifier);
            try
            {
                userId = Guid.Parse(userIdClaim.Value);
                return true;
            }
            catch (FormatException)
            {
                userId = default;
                return false;
            }
        }
    }
}
```
