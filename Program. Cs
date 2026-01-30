using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Hosting;
using System.Security.Cryptography;

var app = WebApplication.CreateBuilder(args).Build();

List<(string key, DateTime expiry, bool used)> keys = new();

string GenerateKey()
{
    byte[] bytes = new byte[16];
    RandomNumberGenerator.Fill(bytes);
    return Convert.ToHexString(bytes);
}

app.MapGet("/generate", () =>
{
    string key = GenerateKey();
    keys.Add((key, DateTime.UtcNow.AddMinutes(10), false));
    return Results.Ok(key);
});

app.MapGet("/verify", (string key) =>
{
    var index = keys.FindIndex(k => k.key == key);

    if (index == -1)
        return Results.Unauthorized();

    var record = keys[index];

    if (record.used || record.expiry < DateTime.UtcNow)
        return Results.Unauthorized();

    keys[index] = (record.key, record.expiry, true);
    return Results.Ok("LOGIN_SUCCESS");
});

app.Run();
