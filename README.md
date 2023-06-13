# Player_Movement
//This is a player movement script that utilizes the Unity input system
using UnityEngine;
using UnityEngine.InputSystem;

public class PlayerMovement : MonoBehaviour
{
    public float speed = 5f;
    public float jumpForce = 5f;

    private Rigidbody2D rb;
    private Vector2 moveInput;
    private bool jumpInput;

    private void Awake()
    {
        rb = GetComponent<Rigidbody2D>();
    }

    private void OnEnable()
    {
        // Assuming you have a PlayerInput component on this object
        var playerInput = GetComponent<PlayerInput>();
        playerInput.actions["Move"].performed += ctx => moveInput = ctx.ReadValue<Vector2>();
        playerInput.actions["Move"].canceled += ctx => moveInput = Vector2.zero;
        playerInput.actions["Jump"].performed += ctx => jumpInput = true;
        playerInput.actions["Jump"].canceled += ctx => jumpInput = false;
    }

    private void OnDisable()
    {
        var playerInput = GetComponent<PlayerInput>();
        playerInput.actions["Move"].performed -= ctx => moveInput = ctx.ReadValue<Vector2>();
        playerInput.actions["Move"].canceled -= ctx => moveInput = Vector2.zero;
        playerInput.actions["Jump"].performed -= ctx => jumpInput = true;
        playerInput.actions["Jump"].canceled -= ctx => jumpInput = false;
    }

    private void FixedUpdate()
    {
        // Apply horizontal movement
        rb.velocity = new Vector2(moveInput.x * speed, rb.velocity.y);

        // Apply jump force
        if (jumpInput && Mathf.Abs(rb.velocity.y) < 0.001f)
        {
            rb.AddForce(new Vector2(0f, jumpForce), ForceMode2D.Impulse);
            jumpInput = false;
        }
    }
}

